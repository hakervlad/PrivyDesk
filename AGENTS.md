# PrivyDesk Agent Workflow

## Modellzuordnung
- **Architect & QA / Haupt-Orchestrator:** `gpt-6-astra`, Reasoning `high`.
- **Git Manager:** `gpt-6-luna`, Reasoning `low`; Issue-, Branch-, Worktree-, Commit-, PR- und Project-Verwaltung, keine Anwendungscode-Änderungen.
- Für T3-Delegationen an den Git Manager das Modell ausdrücklich setzen: Provider `codex`, Modell `gpt-6-luna`, `target.options.reasoningEffort = "low"`. Die Agentendatei allein setzt nicht das Modell einer separaten T3-Delegation.
- `.codex/config.toml` legt den projektlokalen Codex-Standard fest. T3 verwaltet seine Modellauswahl zusätzlich pro Thread; bei neuen Threads `gpt-6-astra` auswählen.
- DeepSeek Harness bleibt der Developer; eine Änderung der Codex-Modellzuordnung startet keinen Developer-Aufruf.

## Rollen
- **Architect & QA (Codex, Hauptagent):** Anforderungen analysieren, passende Architektur und kurzen Plan festlegen, Risiken prüfen, bei neuen Funktionen zuerst Tests schreiben, ausschließlich Implementierung an Developer delegieren, Änderungen reviewen und Verifikation ausführen. Kleine, klar begrenzte Änderungen darf Codex selbst erledigen.
- **Developer (DeepSeek Harness):** Nur die ausdrücklich zugewiesene Implementierungsaufgabe ausführen. `minimal-code` und relevante Skills beachten; keine Issue-Verwaltung, Architektur-Großplanung, Recherche, Feature-Erweiterung oder Agentenstarts. Nur nötige Dateien lesen und ändern.
- **Git Manager (Codex-Subagent, wenn T3 verfügbar):** Verwaltet Issues, Branches, Worktrees, Commits, Pull Requests, Labels und Project-Status. Kein Anwendungscode, keine Architektur, keine eigenen Implementierungsaufträge. Nur der Main Manager darf ihn zu Push/Merge autorisieren. Vor der ersten GitHub-Schreibaktion Nutzerbestätigung einholen; keine Wiederholungsfrage für bereits konkret autorisierte Aktionen. Nicht nach jedem Edit starten.

## Ablauf V2
1. Nutzer gibt eine Aufgabe frei. Nur der Main Manager analysiert Anforderungen, entscheidet Architektur und autorisiert Implementierung.
2. Git Manager sucht bestehende Issues inklusive geschlossener Duplikate. Falls nötig bereitet er ein Issue mit Beschreibung, Akzeptanzkriterien, Priorität, Abhängigkeiten und später Branch/PR vor; Erstellung erst nach Nutzerfreigabe.
3. Ein Issue = ein Feature-Branch = ein Worktree: `feat/<issue-number>-<short-description>` bzw. `fix/<issue-number>-<short-description>`. Vorher bestehende Branches und Worktrees prüfen. Niemals auf `main` entwickeln.
4. Main Manager arbeitet im gebundenen Issue-Worktree, plant kurz, schreibt passende Tests mit `test-driven-development` und beobachtet den erwarteten Fehler vor der Implementierung. Vorhandene Testbefehle verwenden; fehlende Testinfrastruktur explizit klären.
5. Main Manager delegiert ausschließlich die Implementierung an DeepSeek Harness, im selben Worktree, mit `minimal-code` und nur relevanten Pfaden/Anforderungen. Maximal ein aktiver DeepSeek-Auftrag. Für kleine Aufgaben bleibt die bestehende Ausnahme direkter Codex-Implementierung erhalten, ebenfalls im Issue-Worktree mit QA.
6. DeepSeek meldet Ergebnis, geänderte Dateien, Fehler und offene Aufgaben. Er verwaltet weder Dokumentation noch GitHub/Git; keine weitere Agentendelegation, keine Architektur oder Recherche außerhalb der Aufgabe.
7. Main Manager führt Tests und Code-Review aus: Funktionalität, alte/neue Tests, unnötiger Code/Abhängigkeiten, unerwünschte Änderungen, Architektur, Sicherheit und `privacy-review`. Bei Fehlern `systematic-debugging` und konkrete Fehlerauszüge weitergeben; maximal zwei automatische Korrekturrunden. Danach stoppen und Blocker dokumentieren.
8. Falsche Tests dürfen nur nach belegter Fehlannahme korrigiert werden. Keine Testentfernung zur künstlichen Freigabe. Fehlgeschlagene Tests blockieren Freigabe.
9. Erst nach erfolgreicher lokaler Verifikation und Review autorisiert Main Manager Commit/Push des konkret geprüften Diffs. Git Manager organisiert explizite Dateiauswahl, Commit und Feature-Branch-Push über den kontrollierten Ausführungspfad. Kein `git add .`, kein automatischer Push ohne Freigabe.
10. Git Manager erstellt den PR zu `main`, verknüpft das Issue und dokumentiert die Prüfungen. Anschließend PR in T3 verknüpfen. CI muss tatsächlich laufen; fehlende CI ist ein Blocker.
11. Main Manager prüft finalen PR-Head-SHA, Base-Stand, lokale Testergebnisse, CI, Review-Blocker und Konflikte. Freigabe enthält Issue, PR, vollständigen Head-SHA und geprüften Base-SHA. Neue Commits, Konfliktauflösung oder eine relevante Base-Änderung machen die Freigabe ungültig.
12. Nach finaler Main-Manager-Freigabe und zusätzlicher Nutzerbestätigung darf Git Manager den genau freigegebenen Head mergen. Nutzerbestätigung bleibt erforderlich, bis der Nutzer diese Regel ausdrücklich ändert. Optionale automatische Freigabe risikoarmer Änderungen bleibt deaktiviert.
13. Erst nach bestätigtem Merge Issue schließen bzw. GitHub-Schließautomation überprüfen, Project auf Done aktualisieren und sauberen Worktree kontrolliert aufräumen. Bei Unsicherheit stoppen; nach Freigabe Blocker im Issue/Project markieren.

## Zustände, Übergaben und Berechtigungen
- GitHub Issues und PRs sind die dauerhafte Aufgabenverwaltung; keine zusätzlichen Statusdateien pro Issue.
- Lebenszyklus: Backlog → Ready → In Progress → In Review → Ready to Merge → Done; Blocked bei Hindernissen. Vorhandene passende Statuswerte verwenden; keine doppelten Felder. Fehlende Werte erst nach Zustimmung ergänzen. Aktuelle Zuordnung und Voraussetzungen: `docs/development-workflow-v2.md`.
- Skills nur rollenbezogen laden. `superpowers:test-driven-development` und `superpowers:verification-before-completion` in den übernommenen Skills bezeichnen hier die gleichnamigen lokalen Skills ohne Präfix; kein weiteres Framework nötig.
- Übergaben: Issue-Nummer, Aufgabe, Worktree, relevante Dateien, Akzeptanzkriterien, relevante Testergebnisse, gewünschtes Ergebnis. Rückgaben: Ergebnis, geänderte Dateien/Ressourcen, Fehler, offene Aufgaben. Freigaben mit SHA stehen im Feld „gewünschtes Ergebnis“; Main Manager entscheidet weiter.
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
