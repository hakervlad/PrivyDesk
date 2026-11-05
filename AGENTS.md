# PrivyDesk Agent Workflow

## Modellzuordnung
- **Architect & QA / Haupt-Orchestrator:** `gpt-6-astra`, Reasoning `high`.
- **Git Manager:** `gpt-6-luna`, Reasoning `low`; Issue-, Branch-, Worktree-, Commit-, PR- und Project-Verwaltung, keine Anwendungscode-Änderungen.
- Für T3-Delegationen an den Git Manager das Modell ausdrücklich setzen: Provider `codex`, Modell `gpt-6-luna`, `target.options.reasoningEffort = "low"`. Die Agentendatei allein setzt nicht das Modell einer separaten T3-Delegation.
- `.codex/config.toml` legt den projektlokalen Codex-Standard fest. T3 verwaltet seine Modellauswahl zusätzlich pro Thread; bei neuen Threads `gpt-6-astra` auswählen.
- DeepSeek Harness bleibt der Developer; eine Änderung der Codex-Modellzuordnung startet keinen Developer-Aufruf.

## Branch-Trennung
- `main` enthält stabile App-Releases; `dev` ist der Integrationszweig. Neue Feature-/Fix-Branches zweigen von einem geprüften `dev`-Stand ab, ihre PRs zielen auf `dev`. Keine direkte Implementierung auf `main` oder `dev`.
- `ops/agent-workflow` enthält die KI-Infrastruktur. Main Manager bleibt im Ops-Worktree. `.agents/`, `.dsh/`, die gesamte `.codex/`, `AGENTS.md` und reine KI-Orchestrierungsdokumentation dürfen nicht in getrackte App-Branches gelangen.
- Ops und den historischen `chore/multi-agent-workflow` niemals in `dev`/`main` mergen oder deren Konfigurationscommits cherry-picken. Gewöhnliche App-Dokumentation, Tests und CI dürfen später als einzeln geprüfte Änderungen im Feature-Branch entstehen; keine komplette Ops-Historie übernehmen.
- Vor App-Commits und PRs den gesamten getrackten Zielbaum auf KI-Dateien prüfen, nicht nur den Diff. Branch-Trennung ist keine technische Dateisandbox.

## Rollen
- **Architect & QA (Codex, Hauptagent):** Anforderungen analysieren, passende Architektur und kurzen Plan festlegen, Risiken prüfen, bei neuen Funktionen zuerst Tests schreiben, ausschließlich Implementierung an Developer delegieren, Änderungen reviewen und Verifikation ausführen. Kleine, klar begrenzte Änderungen darf Codex selbst erledigen.
- **Developer (DeepSeek Harness):** Nur die ausdrücklich zugewiesene Implementierungsaufgabe ausführen. `minimal-code` und relevante Skills beachten; keine Issue-Verwaltung, Architektur-Großplanung, Recherche, Feature-Erweiterung oder Agentenstarts. Nur nötige Dateien lesen und ändern.
- **Git Manager (Codex-Subagent, wenn T3 verfügbar):** Verwaltet Issues, Branches, Worktrees, Commits, Pull Requests, Labels und Project-Status. Kein Anwendungscode, keine Architektur, keine eigenen Implementierungsaufträge. Nur der Main Manager darf ihn zu Push/Merge autorisieren. Vor der ersten GitHub-Schreibaktion Nutzerbestätigung einholen; keine Wiederholungsfrage für bereits konkret autorisierte Aktionen. Nicht nach jedem Edit starten.

## Gemeinsame Issue-Übernahme
Vor Beginn jeder Aufgabe durch Entwickler oder KI-Agenten das zugehörige Live-Issue
prüfen: Assignees, Status, Absprachen und vorhandener Branch/PR. Bereits zugewiesene
oder in Bearbeitung befindliche Issues nur nach Abstimmung übernehmen; dies gilt
auch für den Freund des Nutzers und dessen Agenten. Bei vereinbarter Übernahme den
abgestimmten menschlichen GitHub-Assignee und Status aktualisieren und zurücklesen.
Bei unklarer Zuständigkeit keine Implementierung. Unmittelbar vor Developer-Start
erneut prüfen; CLI-Updates sind keine atomare Sperre. Main/Git Manager erledigt die
GitHub-Prüfung und übergibt DeepSeek den geprüften Issue-/Zuständigkeitsstand;
DeepSeek übernimmt keine GitHub-Verwaltung. Ohne aktuelle Freigabe nicht starten.

## Ablauf V2
1. Nutzer gibt eine Aufgabe frei. Nur der Main Manager analysiert Anforderungen, entscheidet Architektur und autorisiert Implementierung.
2. Git Manager sucht bestehende Issues inklusive geschlossener Duplikate. Falls nötig bereitet er ein Issue mit Beschreibung, Akzeptanzkriterien, Priorität, Abhängigkeiten und später Branch/PR vor; Erstellung erst nach Nutzerfreigabe.
3. Ein Issue = ein Feature-Branch = ein Worktree: `feat/<issue-number>-<short-description>` bzw. `fix/<issue-number>-<short-description>`, Basis `dev`. Vorher bestehende Branches und Worktrees prüfen und wiederverwenden.
4. Main Manager bleibt an Ops gebunden, plant kurz und schreibt passende Tests im Feature-Worktree mit explizitem Tool-`workdir`/absoluten Pfaden. `test-driven-development` verwenden und den erwarteten Fehler vor Implementierung beobachten. Vorhandene Testbefehle verwenden; fehlende Testinfrastruktur explizit klären.
5. Main Manager beauftragt ausschließlich Implementierung durch DeepSeek im selben Feature-Worktree wie die Tests. Dafür den gewünschten Developer-Thread mit `t3_thread_launch` und `workspaceStrategy.type = "existing_worktree"` an den absoluten Feature-Pfad binden; kein `delegate_task isolation=shared` aus Ops. Die vollständigen kurzen `minimal-code`-Regeln aus `docs/development-workflow-v2.md` im Auftrag mitgeben; Ops-Skills sind dort nicht automatisch verfügbar. Vor Schreiben tatsächliches cwd/Branch/Head prüfen. Maximal ein aktiver DeepSeek-Auftrag. Kleine direkte Codex-Implementierungen bleiben mit QA im Feature-Worktree erlaubt.
6. DeepSeek meldet Ergebnis, geänderte Dateien, Fehler und offene Aufgaben. Er verwaltet weder Dokumentation noch GitHub/Git; keine weitere Agentendelegation, keine Architektur oder Recherche außerhalb der Aufgabe.
7. Main Manager führt Tests und Code-Review aus: Funktionalität, alte/neue Tests, unnötiger Code/Abhängigkeiten, unerwünschte Änderungen, Architektur, Sicherheit und `privacy-review`. Bei Fehlern `systematic-debugging` und konkrete Fehlerauszüge weitergeben; maximal zwei automatische Korrekturrunden. Danach stoppen und Blocker dokumentieren.
8. Falsche Tests dürfen nur nach belegter Fehlannahme korrigiert werden. Keine Testentfernung zur künstlichen Freigabe. Fehlgeschlagene Tests blockieren Freigabe.
9. Erst nach erfolgreicher lokaler Verifikation und Review autorisiert Main Manager Commit/Push des konkret geprüften Diffs. Git Manager organisiert explizite Dateiauswahl, Commit und Feature-Branch-Push über den kontrollierten Ausführungspfad. Kein `git add .`, kein automatischer Push ohne Freigabe.
10. Git Manager erstellt den PR zu `dev`, verknüpft das Issue durch explizite gegenseitige Links und dokumentiert die Prüfungen. `Closes` garantiert bei diesem Nicht-Default-Ziel keinen Abschluss. Anschließend PR in T3 verknüpfen. CI muss tatsächlich laufen; fehlende CI ist ein Blocker.
11. Main Manager prüft finalen PR-Head-SHA, Base-Stand, lokale Testergebnisse, CI, Review-Blocker und Konflikte. Freigabe enthält Issue, PR, vollständigen Head-SHA und geprüften Base-SHA. Neue Commits, Konfliktauflösung oder eine relevante Base-Änderung machen die Freigabe ungültig.
12. Nach finaler Main-Manager-Freigabe und zusätzlicher Nutzerbestätigung darf der genau freigegebene Feature-Head nach `dev` gemergt werden. Releases nach `main` benötigen einen separaten Auftrag und eigene Prüfung. Nutzerbestätigung bleibt erforderlich, bis der Nutzer diese Regel ausdrücklich ändert. Optionale automatische Freigabe risikoarmer Änderungen bleibt deaktiviert.
13. Erst nach bestätigtem Merge Issue schließen bzw. GitHub-Schließautomation überprüfen, Project auf Done aktualisieren und sauberen Worktree kontrolliert aufräumen. Bei Unsicherheit stoppen; nach Freigabe Blocker im Issue/Project markieren.

## Zustände, Übergaben und Berechtigungen
- GitHub Issues und PRs sind die dauerhafte Aufgabenverwaltung; keine zusätzlichen Statusdateien pro Issue.
- Lebenszyklus: Backlog → Ready → In Progress → In Review → Ready to Merge → Done; Blocked bei Hindernissen. Vorhandene passende Statuswerte verwenden; keine doppelten Felder. Fehlende Werte erst nach Zustimmung ergänzen. Aktuelle Zuordnung und Voraussetzungen: `docs/development-workflow-v2.md`.
- Skills nur rollenbezogen laden. `superpowers:test-driven-development` und `superpowers:verification-before-completion` in den übernommenen Skills bezeichnen hier die gleichnamigen lokalen Skills ohne Präfix; kein weiteres Framework nötig.
- Übergaben: Issue-Nummer, Aufgabe, absoluter Worktree, Branch und vollständiger Basis-/Start-Head, relevante Dateien, Akzeptanzkriterien, relevante Testergebnisse, gewünschtes Ergebnis. Rückgaben: Ergebnis, geänderte Dateien/Ressourcen, Fehler, offene Aufgaben. Freigaben mit SHA stehen im Feld „gewünschtes Ergebnis“; Main Manager entscheidet weiter.
- Git Manager verwendet `github-project-management` und `git-worktree-pr`. Die bisherige `sandbox_mode = "read-only"`-Angabe bleibt erhalten, erzwingt bei Custom Agents nach aktueller Codex-Dokumentation aber keine eigene Sandbox. Elternlaufzeit/Host muss die Grenze tatsächlich setzen. Ohne überprüfte Beschränkung nur Befehle vorbereiten, keine Schreibausführung; Main Manager führt konkret freigegebene Aktionen aus. Ein technisch eingeschränkter Git-Executor ist nur vorgeschlagen, nicht installiert.
- Lokale Git-Schreibrechte, GitHub-Token-Rechte, Main-Manager-Freigabe und Nutzerbestätigung sind getrennte Voraussetzungen. Kein Agent darf Berechtigungen selbst erweitern oder Freigaben umgehen.
- T3-Worktree-Bindung und DeepSeek-Delegation gemäß `docs/development-workflow-v2.md`; neue Top-Level-Threads nur auf Nutzerwunsch. Eine Delegation allein ist kein Auftrag für einen neuen Top-Level-Thread.

## Kosten und parallele Arbeit
- DeepSeek nur für Implementierung; keine Aufrufe ohne klaren Auftrag. Aufgaben klein und mit relevanten Pfaden/Anforderungen übergeben; keine vollständige Repo-Analyse verlangen.
- Keine unnötigen Subagenten; keine parallelen Änderungen derselben Dateien. Kleine Aufgaben möglichst mit einem Agenten bearbeiten.
- Kurze Statusberichte. Skills nur laden, wenn relevant.
- Keine festen Guthabenlimits behaupten. Vor einer DeepSeek-Aufgabe DSH-/Provider-Usage-Dashboard prüfen; der Harness garantiert aus Projektdateien heraus keine Begrenzung. Keine kostenpflichtigen Testaufrufe.

## Sicherheit und Berechtigungen
- Keine Schlüssel oder Zugangsdaten in Projektdateien oder Ausgaben. Keine echten Nutzerdokumente in Tests.
- Technische Sandbox- und Dateiberechtigungen sind durch Laufzeit/Host-Einstellungen festgelegt; Promptregeln allein erzwingen keine Sandbox. Vor Ausführung Berechtigungen respektieren und notwendige Grenzen nicht als technisch garantiert darstellen.
- Datenschutzprüfung bei relevanten Änderungen: Dateirechte, sichere Dateiverarbeitung, Nutzerbestätigung, externe KI-Datenübertragung, Zugriff außerhalb freigegebener Verzeichnisse und Risiken bei Dateiänderung/-löschung.
- Keine globalen Konfigurationen oder Framework-Installationen ohne ausdrücklichen Auftrag. Pushes nur mit konkreter Main-Manager-Freigabe, Merges zusätzlich mit Nutzerbestätigung. Keine Force-Pushes auf `main`, Schutzregel-Umgehung, CI-Überspringung oder ungefragte Änderung fremder PRs.
