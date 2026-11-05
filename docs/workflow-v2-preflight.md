# Technische Vorprüfung und Simulation – Workflow V2

Prüfdatum: 2026-11-05. Dies ist eine einmalige Einrichtungsaufnahme, kein zweites
Aufgaben-/Statussystem. Laufende Aufgaben werden ausschließlich in Issues/PRs gepflegt.

## 1. Rollen und Modelle

| Rolle | Modell / Laufzeit | Status |
| --- | --- | --- |
| Main Manager / Architect & QA | Codex gpt-6-astra, high | Projektkonfiguration und T3-Modellauswahl gelesen/zurückgelesen; so eingestellt |
| Git Manager | Codex gpt-6-luna, low | Custom-Agent-TOML vorhanden, Syntax und lokaler Modellkatalog geprüft; kein Agent gestartet |
| Developer | DeepSeek Harness 0.2.1-rc.5, dsh --profile acp | Paket, ACP-Profil, T3-Registrierung und Launch-Pfad lokal geprüft; kein API-/Modellaufruf |

DSH ist direkt als ACP-Provider `acpRegistry_deepseek` registriert, aktiviert und
laut T3 delegierbar; Codex ebenfalls. ACP-App und Basis-Paket sind installiert.
DeepSeek-Flash- und Pro-Modelle erscheinen im Provider-Katalog. Der Workflow schlägt
für den ersten späteren Auftrag den offiziellen Flash-API-Eintrag mit low vor;
Abrechnung und Modellwahl sind vor tatsächlicher Nutzung zu bestätigen. Kein
API-Schlüssel, keine Konto- oder Endpoint-Erreichbarkeit und kein Kontostand wurden
mit einem Modellaufruf geprüft. „Registriert“ ist kein Nachweis einer funktionierenden
Implementierungsdelegation.

## 2. Skills und Konfiguration

Vorhanden und Frontmatter mit PyYAML erfolgreich geprüft:

- .agents/skills/test-driven-development/SKILL.md
- .agents/skills/verification-before-completion/SKILL.md
- .agents/skills/systematic-debugging/SKILL.md
- .agents/skills/privacy-review/SKILL.md
- .agents/skills/github-project-management/SKILL.md
- .dsh/skills/minimal-code/SKILL.md
- **Neu:** .agents/skills/git-worktree-pr/SKILL.md

Die drei ursprünglichen Superpowers-SKILL.md-Dateien blieben unverändert und stimmen
mit Upstream-Commit `22f0b463e7a5e579904dd6211a520321035526f7` überein. Zuvor fehlende
Begleitdateien wurden gelesen und ergänzt: writing-good-tests.md, root-cause-tracing.md,
defense-in-depth.md, condition-based-waiting.md sowie dessen TypeScript-Beispiel und
find-polluter.sh. Kein externes Skript wurde ausgeführt; bash -n prüfte nur Syntax.
Das npm-spezifische Hilfsskript ist ohne passenden Stack hier nicht als Test verwendbar.
Quellnachweis und Lizenz liegen unter .agents/skills/; kein komplettes Framework.

TOML mit Python tomllib erfolgreich geparst; Name, Beschreibung, Instruktionen,
Modelle und Reasoning-Werte lokal geprüft. `codex --strict-config app-server
--print-startup-config` endete mit 0 und lieferte gültiges JSON, ohne App-Server- oder
Modellstart. Der Versuch `codex --strict-config features list` ist in dieser Version
nicht unterstützt; er gilt ausdrücklich nicht als bestandene Prüfung. Die separate
lokale Feature-Abfrage meldet multi_agent stable/true. Custom-Agent-Laufzeitverhalten
und Skillbefolgung wurden nicht durch einen Agentenstart geprüft.

## 3. Orchestrierung und Worktrees

orchestrator_capabilities wurde gelesen. T3 meldet App-Subagents, Cross-Provider-
Delegation, Abbruch und Thread-Verwaltung. Tool-Schemas vorhanden für delegate_task,
task_cancel, t3_thread_launch mit worktree/existing_worktree und t3_worktree_handoff;
außerdem PR-Link-/Watch-Werkzeuge. Kein neuer Thread/Child oder Worktree wurde
angelegt. Die tatsächliche Thread-Bindung eines neuen Worktrees bleibt ungetestet.

Git hat lokal main und Remote-Tracking-Branches origin/main und origin/dev. GitHub
bestätigt main/dev, beide ungeschützt. Ein einziger vorhandener Worktree: das
Projektverzeichnis auf main, initialer Commit f94bc6cf7646a8c4485282f9dfb7c7119f6dab5b.
Neue Entwicklung verwendet feat/<issue>-<slug> bzw. fix/<issue>-<slug>; Zuordnung und
T3-Bindung sind in development-workflow-v2.md beschrieben.

`gh issue develop --help` ist geprüft: Branch-Verknüpfung ja, Worktree-Flag nein.
Es schreibt einen Remote-Branch. Deshalb ist lokale/T3-Worktree-Erstellung vor QA
der Standard; der erste Push bleibt bis Freigabe gesperrt.

Wichtig: Alle Agenten-/Workflow-Dateien sind momentan uncommittet. Sie werden nicht
von selbst in neue Worktrees kopiert. Erst einen freigegebenen Bootstrap-Branch mit
geprüftem Konfigurations-Commit organisieren. Während dieser Einrichtung wurden
weder Branches erstellt noch Commits angelegt oder gepusht.

## 4. GitHub und Project

gh 2.45.0 ist installiert und am Account hakervlad angemeldet. Lesende REST- und
GraphQL-Abfragen auf hakervlad/PrivyDesk waren erfolgreich. Repository öffentlich,
Default main; viewerPermission ADMIN, push/admin/maintain/triage/pull true.
Token-Scopes enthalten repo, project, read:org (zusätzlich bestehende andere Scopes).
Keine Credentials ausgegeben; keine Schreibprobe durchgeführt.

User-Project 1 ist privat, offen, unbenannt, 0 Items, 19 Felder. Feld-/Optionszugriff
funktioniert. Das Repository hat noch keine projectsV2-Verknüpfung. Status enthält
Backlog, Ready, In progress, In review, Done; Ready to Merge und Blocked fehlen.
Priority enthält P0/P1/P2. Remote-Felder, Optionen und Verknüpfungen wurden unverändert
belassen. Auswahl des Projects und Ergänzungen benötigen Nutzerzustimmung.
Project-Automationen wurden nicht geprüft; deren Vorhandensein wird nicht behauptet.
Es gibt derzeit keine Issues oder PRs im Repository (Issue-REST-Liste leer).

## 5. CI, Schutz und Merge-Berechtigungen

Lokal nur README und Agenten-/Workflow-Konfiguration; keine Stack-Manifeste,
Anwendungstests, Lint- oder Typecheck-Befehle. GitHub listet 0 Actions-Workflows.
Die neue .github/workflow-templates/privydesk-ci.yml.template ist inaktiv; YAML,
PR-Trigger, minimale contents:read-Berechtigung und expliziter Abbruch bei fehlenden
Voraussetzungen wurden lokal geprüft. Kein grüner Testlauf/CI-Lauf wird behauptet.

main-Protection: GitHub 404 „Branch not protected“. Rulesets: leere Liste.
Auto-Merge false. Merge-, Rebase- und Squash-Methode sind serverseitig erlaubt.
Admin-/Push-Rechte deuten technische Merge-Möglichkeit an; ein echter Merge wurde
nicht versucht. gh unterstützt --match-head-commit (lokale Hilfe gelesen).
Keine Remote-Einstellungen wurden geändert. Bis echte CI und vereinbarter Schutz
vorhanden sind, bleibt der Entwicklungsworkflow vor dem Merge blockiert.

## 6. Technische Rechte und verbleibende Grenzen

Keine fehlende gh-Lese-/Project-Berechtigung festgestellt. Die vorhandenen Admin-
und Token-Rechte sind breiter als die fachliche Git-Manager-Rolle. Promptregeln
schränken diese Rechte nicht technisch ein.

Die Git-Manager-Datei behält read-only als Einschränkungsabsicht. Laut offizieller
Codex-Dokumentation überschreibt sandbox_mode in Custom-Agent-Dateien nicht die
Eltern-Sandbox. Die aktuelle Hauptlaufzeit ist full-access. Isolierter Git-Manager-
Schreibschutz wurde daher nicht nachgewiesen. Auch ein Dateischreibschutz allein
verhindert keine schreibenden GitHub-API-Aufrufe.

Bis ein Host-Ausführungspfad nachgewiesen ist, bereitet der Git Manager Änderungen
vor und Main Manager führt ausschließlich konkret freigegebene Befehle aus. Ein
Allowlist-Executor mit getrennten Credentials ist beschrieben, aber weder installiert
noch getestet. Keine Sandbox wurde pauschal erweitert. Kein neuer Agent erstellt.

## 7. Simulation ohne Ausführung

Fiktives Issue #999999 „Minimaler Vorschau-Dialog“, fiktiver Branch
feat/999999-minimal-preview; weder abgefragt noch erstellt. Alle nachfolgenden
Erfolgsszenarien sind hypothetisch und ersetzen keine realen Tests.

1. Main Manager würde Anforderungen und datensparsame Akzeptanzkriterien definieren;
   Git Manager bereitet Issue und Branch-Zuordnung vor. Nutzerfreigabe vor Erstellung.
2. Für den **tatsächlichen Ist-Stand** stoppt der vollständige Testlauf bereits an
   Bootstrap-/Project-/Stack-Voraussetzungen. Es entsteht kein Worktree und kein
   DeepSeek-Aufruf. Kein Übergang zu Ready to Merge oder Done.
3. Nach hypothetisch erfüllten Voraussetzungen: gebundener Issue-Worktree, Plan,
   nachgewiesen fehlschlagender Test, ein shared-DeepSeek-Auftrag, Antwort mit
   geänderten Pfaden, Main-Manager-Tests/Review; höchstens zwei Korrekturrunden.
4. Hypothetisches QA bestanden: Main Manager gibt konkreten Diff/Commit frei;
   Git Manager organisiert kontrollierten Commit, Push und PR mit Issue-Link.
5. CI fehlt/fehlschlägt oder ein Review-Blocker besteht → kein Merge. Head ändert
   sich nach Review → alte Freigabe verwerfen. Konflikt → an Main Manager zurück.
6. Nur wenn finaler Head/Base geprüft, alle Gates bestanden und Nutzermergefreigabe
   vorhanden: SHA-gebundener Merge; erst danach Issue-Abschluss, Project Done,
   sauberen Worktree nach Prüfung aufräumen.

Dies ist eine dokumentarische Simulation. Es wurden keine Agentenbefolgung,
Schreibrechte, Remote-Änderungen, CI oder Modellantworten simuliert und als echt
gewertet. Kein End-to-End-Test hat stattgefunden.

## 8. Konkreter nächster Testdurchlauf

1. Konfiguration reviewen und als erste reale Aufgabe einen Bootstrap-Branch/Issue
   freigeben; Commit/Push/PR erst mit konkreter Main-Manager-Freigabe durchführen.
2. Bestätigen, ob User-Project 1 für PrivyDesk verwendet werden soll; dann seine
   Automationen prüfen und die zwei fehlenden Optionen sowie Repo-Verknüpfung
   nach Zustimmung am bestehenden Feld/Project ergänzen.
3. Technologie-Stack und echte lokale Prüfkommandos wählen; CI-Vorlage implementieren,
   aktivieren, einen realen PR-Lauf prüfen und main-Schutz gesondert autorisieren.
4. Technisch eingeschränkten Git-Ausführungspfad verifizieren; sonst kontrollierte
   Ausführung durch Main Manager beibehalten. Keine angeblich isolierte read-only-
   Rolle mit unverändert breiten Host-/GitHub-Rechten annehmen.
5. Kleines echtes Feature/Fehler-Issue und erwartete Kriterien freigeben. Bei gewünschtem
   separatem T3-Thread dies ausdrücklich beauftragen; Worktree-Bindung vor TDD prüfen.
6. Guthaben im Provider-Dashboard prüfen und ersten kostenpflichtigen DeepSeek-Auftrag
   ausdrücklich als echten Test freigeben. Kein kostenpflichtiger „Ping“ nötig.
7. Nach lokalem QA kontrolliert pushen/PR erstellen; CI und finalen SHA prüfen und
   Nutzerbestätigung für den Merge einholen. Erst dann Abschluss/Aufräumen.

## Quellen und Prüfgrenzen

- Codex-Agentenfelder und vererbte Sandbox: https://learn.chatgpt.com/docs/agent-configuration/subagents
- Modell-/Reasoning-Werte: lokale Codex-Modellliste; Projekt-TOML und T3-Auswahl
- T3-Fähigkeiten: gelesene Tool-Schemas und orchestrator_capabilities, kein Kindstart
- GitHub: ausschließlich lesende gh-REST-/GraphQL-/Project-Abfragen
- DSH: installierte Paket-README, Paketmetadaten, ACP-Profil und T3-Launch-Konfiguration

Keine DeepSeek-Modellanfrage, keine Test-Issues, Pushes, Merges, Änderungen an
GitHub-Einstellungen oder Anwendungscode. Keine globalen Einstellungen verändert.
