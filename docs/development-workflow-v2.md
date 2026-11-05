# PrivyDesk Entwicklungsworkflow V2

## Rollen und Entscheidungsrechte

Main Manager: Codex `gpt-6-astra`, Reasoning `high`. Er verantwortet Anforderungen,
Architektur, Plan, TDD, DeepSeek-Aufträge, Review und Freigaben.
Git Manager: Codex `gpt-6-luna`, Reasoning `low`. Er organisiert Issues, Branches,
Worktrees, Commits, PRs und Projects ausschließlich auf Auftrag; kein Anwendungscode.
Developer: DeepSeek Harness über `dsh --profile acp`, keine OpenCode-Zwischenschicht.
Er implementiert nur abgegrenzte Aufgaben und konkrete Korrekturen im Issue-Worktree.
Es gibt keine weitere Agentenrolle. QA bleibt beim Main Manager.
Der Nutzer hat Tauri 2 + React + TypeScript + Rust ausdrücklich bestätigt;
Toolchain-Installationen und Implementierung sind noch nicht gestartet.

Nur Main Manager autorisiert Implementierung, kennzeichnet Code als geprüft und gibt
Push/Merge frei. Für finale Merges ist zusätzlich die Nutzerbestätigung erforderlich.
Automatische Freigabe, Auto-Merge und Merge-Queue-Automatisierung sind nicht aktiviert.
Eine spätere Option für risikoarme Änderungen benötigt eine ausdrückliche neue
Nutzerfreigabe, definierte Risikokriterien und unverändert alle SHA-/CI-/Review-Gates.

## Issue-Lifecycle und vorhandenes Project

Prüfstand 2026-11-05: Repository `hakervlad/PrivyDesk`, öffentlich, Default `main`.
Das private User-Project [Project 1](https://github.com/users/hakervlad/projects/1)
heißt derzeit `@hakervlad's untitled project`, hat 0 Items und 19 Felder. Es ist
mit dem Repository verknüpft; die zwei ergänzten Statusoptionen wurden nach
Nutzerfreigabe zurückgelesen. Vor Issue-Veröffentlichung fehlen weiterhin die
konkreten Bedingungen der vier Automationen, nicht die Project-Auswahl.

| Workflow | Vorhandener Status | Übergangsvoraussetzung |
| --- | --- | --- |
| Backlog | Backlog | Aufgabe beschrieben |
| Ready | Ready | Akzeptanzkriterien, Priorität und Abhängigkeiten geklärt, Main Manager gibt frei |
| In Progress | In progress | Ein Issue-Branch und gebundener Worktree vorhanden, Auftrag aktiv |
| In Review | In review | Implementierung zurückgegeben, Main Manager prüft |
| Ready to Merge | Ready to Merge | Lokale Tests/Review und tatsächliche finale CI erfolgreich; exakter Head freigegeben |
| Done | Done | Merge und Issue-Abschluss bestätigt |
| Blocked | Blocked | Konkreter Blocker, Verantwortlicher und nächster notwendiger Schritt bekannt |

Bestehendes Statusfeld `PVTSSF_lAHOBPiNWs4AotimzggPN_g` weiterverwenden. Keine
zweiten Felder anlegen. Alle sieben benötigten Optionen sind vorhanden.
Vor jeder Mutation IDs aktuell lesen, nicht dauerhaft auf kopierte IDs vertrauen.
Priority ist vorhanden mit P0/P1/P2; keine neuen Prioritätsfelder/Labels erforderlich.
Keine bestehende Blocked-Label-Konvention gefunden. Die lesende Workflow-Abfrage
zeigt vier aktivierte Project-Automationen: Auto-add sub-issues to project, Auto-close
issue, Item closed und Pull request merged. Ihre konkreten Trigger-/Statuskonfigurationen
sind im gelesenen GraphQL-Typ nicht exponiert und müssen vor Änderungen in der
Project-Oberfläche geprüft werden. Vorhandene passende Automationen bevorzugen;
Done nie vor bestätigtem Merge setzen, insbesondere wegen Auto-close issue.

Vor Implementierungsbeginn gilt die gemeinsame Issue-Übernahmeregel aus AGENTS.md
und github-project-management: Live-Issue/Assignees/Status lesen, bestehende Zuständigkeit
nur nach Abstimmung ändern, vereinbarte Übernahme mit Assignee/Status zurücklesen.
Unklare Zuständigkeit oder zwischenzeitliche Übernahme durch den Freund/seine Agenten
blockiert den Start. Main/Git Manager prüft unmittelbar vor Dispatch; DeepSeek erhält
den bestätigten Issue-Link und Zuständigkeitsstand, keine GitHub-Verwaltungsaufgabe.

Issue-Suche schließt offene und geschlossene ähnliche Aufgaben ein. Ein Issue
enthält Titel, Beschreibung, prüfbare Akzeptanzkriterien, Priorität, bekannte
Abhängigkeiten sowie Branch und PR sobald vorhanden. Vorlagen unter `.github/`
unterstützen dies nach Commit/Push. Issues/PRs bleiben die dauerhafte Dokumentation;
dieses Dokument ist eine Workflow-Anleitung, keine Aufgabenstatusdatei.

## Branch, Worktree und tatsächliche T3-Bindung

`main` enthält stabile Releases; `dev` ist der Integrationszweig der App.
`ops/agent-workflow` enthält ausschließlich die separat gepflegte KI-Infrastruktur
und bleibt der dauerhafte Checkout des Main Managers. Der historische Branch
`chore/multi-agent-workflow` bleibt als Sicherung erhalten.

Ein Issue = ein Branch = ein Worktree. Features `feat/<N>-<slug>` und Fehler
`fix/<N>-<slug>` beginnen beim ausdrücklich geprüften aktuellen `dev`-Stand;
PR-Ziel ist `dev`. Vor Anlage lokale/Remote-Abweichungen, Issue-Verknüpfungen,
Branches, PRs und `git worktree list --porcelain` prüfen. Vorhandenen Worktree
wiederverwenden; keine fremde/uncommittete Arbeit überschreiben oder zurücksetzen.
Nicht direkt auf main/dev entwickeln. Releases dev → main sind separate Aufträge.

`.agents/`, `.dsh/`, `.codex/` einschließlich config.toml, `AGENTS.md` und reine
KI-Orchestrierungsdokumentation bleiben in Ops. Niemals Ops oder die historische
Chore-Konfigurationshistorie nach dev/main mergen oder deren Commits cherry-picken.
Gewöhnliche App-Dokumentation, Tests und Build-/CI-Konfiguration können später als
geprüfte Einzeländerungen im App-Feature entstehen. Vor Commit/PR den vollständigen
getrackten App-Baum zusätzlich zum Diff prüfen; Branches isolieren keine OS-Rechte.

`gh issue develop` 2.45.0 kennt `--name`, `--base`, `--checkout`, `--list`, aber
keine Worktree-Option. Es erzeugt bereits einen Remote-Branch und braucht damit
GitHub-Schreibfreigabe. Standard hier: zunächst lokaler Branch/Worktree; erster
Push erst nach QA. Issue/Branch zunächst im Issue-Text verknüpfen, PR später mit
dem Issue verbinden. gh issue develop ist nur eine ausdrücklich autorisierte
Alternative, wenn der frühere Remote-Branch gewünscht ist.

Für eine **explizit vom Nutzer gewünschte neue Top-Level-T3-Sitzung** kann T3 den
Worktree selbst mit `t3_thread_launch` und
`workspaceStrategy={type:"worktree",branch:<branch>,baseRef:<approved-base>,startFromOrigin:false}`
anlegen. Vorher Duplikate prüfen. Bei einem bereits organisierten Worktree stattdessen
`workspaceStrategy={type:"existing_worktree",worktreePath:<absolute-path>}` verwenden.
Nicht einen zweiten Checkout anlegen. Kein Worktree-Setup in den Aufgabenprompt
verlagern. Thread starten ist kein normaler Ersatz für `delegate_task`.

Der Main Manager wurde mit `t3_worktree_handoff` (Basis 27730d1,
`runSetupScript:false`) nach Ops verschoben. Seine Sitzung bleibt dort. Für TDD,
Tests und Review im Feature immer explizites `exec_command.workdir` oder `git -C`
mit absolutem Feature-Pfad nutzen; Dateiedits ebenfalls am überprüften absoluten
Feature-Pfad. Ein Shell-cd bindet keinen T3-Thread um. Vor jeder Aktion cwd,
Top-Level, Branch und Head des Ziel-Checkouts prüfen, nicht den Ops-Head als
Implementierungsstand verwenden. Main und Developer bearbeiten dieselben
Feature-Dateien zeitlich nacheinander, nie parallel.

Neue Feature-Worktrees enthalten bewusst keine Ops-Skills. Die Regeln werden im
gezielten Auftrag übergeben, nicht in getrackte App-Dateien kopiert. Main liest seine
Skills aus Ops. Die frühere Bootstrap-Voraussetzung „Agentenkonfiguration muss im
Feature-Basiscommit liegen“ entfällt ausdrücklich. Pfade und geprüfter Einrichtungsstand:
[start-readiness.md](start-readiness.md).

## Delegation und knappe Kommunikation

T3 meldet `acpRegistry_deepseek` mit `driverKind=acpRegistry`,
`canRunChildTask=true`, `canRunCrossProviderChildTask=true`. Die lokale
Provider-Konfiguration zeigt `commandPath=dsh`, `commandArgs=["--profile","acp"]`.
DSH und ACP-Profil sind installiert. Das ist eine lokale Registrierungsprüfung;
API-Schlüsselgültigkeit, Netzwerkerreichbarkeit des Modellendpunkts und Guthaben
wurden nicht per Aufruf getestet.

Für den vom Nutzer gewünschten Developer-Thread-Ablauf verwendet Main künftig
`t3_thread_launch` mit `workspaceStrategy={type:"existing_worktree",worktreePath:<absoluter Feature-Pfad>}`.
T3 bindet den schon existierenden Projekt-Worktree vor dem Start. Keine zusätzliche
Worktree-Anlage im Prompt. Provider im `modelSelection.instanceId` ausdrücklich
`acpRegistry_deepseek`; Modell und dessen Optionen zuvor aus dem verfügbaren Katalog
bestätigen. Der zuvor gelesene kostenorientierte Eintrag war
`["deepseek-official","deepseek-flash"]` (Modell-ID als JSON-String) mit
`reasoning_effort=low`; keine behauptete Guthabengarantie, keine ungefragte
Modellsubstitution. Kein Developer-Thread oder Modelltest während der Einrichtung.

**Nicht `delegate_task isolation:"shared"` aus Ops verwenden:** das wäre der
Ops-Checkout. `delegate_task` hat keinen Parameter für einen vorhandenen Worktree.
Ein neuer Top-Level-Thread setzt einen Nutzerauftrag voraus; dieser Developer-Ablauf
ist gewünscht, sein konkreter Implementierungsstart braucht weiterhin eine freigegebene
Aufgabe. Vor jedem Start offene Entwicklerläufe prüfen: maximal einer gleichzeitig.

Nach Start mit `t3_thread_read` Metadaten und Ergebnis lesen (`activity` für relevante
Werkzeugausgaben). Developer muss vor Schreibzugriff echte cwd/Top-Level/Branch/Head
mit dem Auftrag abgleichen. Bei Abweichung stoppen; Metadaten allein genügen nicht.
Main verifiziert Diff und Testergebnisse direkt im Feature-Worktree. Bei unmittelbarem
Ergebnisbedarf `t3_thread_wait` verwenden und dessen `next` befolgen; keine Pollschleifen.
Einen nicht mehr benötigten Top-Level-Developer-Thread mit `t3_thread_interrupt`
beenden. `task_cancel` ist für echte `delegate_task`-Läufe vorgesehen; deren `next`
befolgen, nicht Kind-Threads direkt unterbrechen. Neue delegierte Reviews sind neue
Aufträge, keine `t3_thread_send`-Nachrichten. QA führt Main selbst aus.

Folgende vollständigen Minimal-Code-Regeln im Implementierungsauftrag mitgeben,
nicht nur einen im Feature nicht vorhandenen Skill-Namen nennen:

```text
Wähle die einfachste korrekte, sichere Lösung. Verwende vorhandene Funktionen und
Muster. Keine unnötigen Abstraktionen, Abhängigkeiten, Zusatzfunktionen oder Änderungen
außerhalb des Auftrags. Lies nur relevante Dateien. Respektiere bestehende Tests und
Konventionen; ändere Tests nur im zugewiesenen Umfang. Opfere niemals Sicherheit für
kürzeren Code. Nur Main Manager erteilt Implementierungsaufträge.
Prüfe den mitgegebenen aktuellen Issue-Link, bestätigten Assignee/Status und die
Übernahmefreigabe; bei fehlendem/unklarem Stand Main fragen und nicht beginnen.
Keine fremd zugewiesene oder laufende Aufgabe ohne dokumentierte Abstimmung übernehmen.
Main/Git Manager erledigt dazu die Live-GitHub-Prüfung, nicht der Developer.
Prüfe vor Änderungen echtes cwd, Git-Top-Level, Branch und Head gegen den Auftrag;
bei Abweichung oder auf main/dev/ops stoppen. Arbeite nur im angegebenen Feature-Worktree.
Keine GitHub-Verwaltung, Architektur-Großplanung, allgemeine Recherche oder Agentenstarts.
Keine Commits, Pushes, Merges, Branch-/Worktree-Verwaltung oder Änderungen an Workflow-
Konfiguration und aufgabenfremder Dokumentation. Keine Agentendateien in App-Branches
kopieren. Keine Tests abschwächen; belegbar falsche Tests an Main melden. Keine
QA-Freigabe erteilen. Keine Schlüssel/echten Nutzerdokumente; keine unautorisierten
Dateizugriffe, destruktiven Aktionen oder sensiblen Uploads. Rückgabe kurz: Ergebnis,
geänderte Pfade, ausgeführte Prüfungen, Fehler, offene Aufgaben.
```

Git-Manager-T3-Delegation erhält ausdrücklich Provider codex, Modell gpt-6-luna,
`target.options.reasoningEffort:"low"`. Native Custom-Agent-Dateien werden von T3
nicht automatisch als eigene Rollenbegrenzung garantiert; Anweisungen mitgeben und
tatsächliche Laufzeit prüfen. Neue Kindaufgaben erhalten nur ihren expliziten Prompt.

Übergabeschema für alle Rollen:

```text
Issue: #N + URL; bestätigter Assignee/Status und Übernahmeabstimmung
Aufgabe: ...
Worktree: absoluter Feature-Pfad
Branch: feat/... oder fix/...
Basis: dev + vollständiger geprüfter Basis-SHA
Start-Head: vollständiger aktueller Feature-SHA
Relevante Dateien: ...
Akzeptanzkriterien: ...
Testergebnisse: Befehl, Ergebnis oder noch nicht ausgeführt
Gewünschtes Ergebnis: ...; gegebenenfalls autorisierte Aktion + Head/Base-SHA
```

Rückgabe: Ergebnis, geänderte Dateien/GitHub-Ressourcen, Fehler, offene Aufgaben.
Keine langen Agentengespräche; nur Main Manager entscheidet die nächsten Schritte.
Maximal zwei automatische Korrekturrunden. Bei Fehlern `systematic-debugging` laden,
konkrete relevante Auszüge weitergeben und danach erneut verifizieren. Tests nicht
entfernen, um Fehler zu verdecken. Nach zwei Runden stoppen und Blocker festhalten.

## Rechte und kontrollierte Git-Ausführung

Vier getrennte Ebenen beachten:

| Ebene | Tatsächlicher Stand / Grenze |
| --- | --- |
| Lokale Git-Rechte | Hauptsitzung full-access; OS kann Projekt/.git schreiben. Das ist keine Freigabe. |
| GitHub-API | gh-Account hakervlad hat ADMIN; Token enthält repo, project, read:org. Rechte sind breit; frühere autorisierte Project-Änderungen und Chore-Push bestätigt, kein Merge-Test. |
| Main-Manager-Freigabe | Auftrag mit Issue, Pfaden, Aktion und geprüftem Stand; keine technische Sandbox. |
| Nutzerbestätigung | Erste GitHub-Schreibaktion und jeder finale Merge bis ausdrücklicher Policy-Änderung; Repository-Einstellungen separat bestätigen. |

Die vorhandene Agentendatei behält `sandbox_mode="read-only"`. Nach der
[offiziellen Custom-Agent-Dokumentation](https://learn.chatgpt.com/docs/agent-configuration/subagents)
überschreibt dieses Feld bei Custom Agents die Eltern-Sandbox nicht. Daher wird
kein technisch erzwungener Schreibschutz behauptet. T3 `approval-required` ist ein
Freigabemodus und ebenfalls keine Garantie für eine reine Lesesandbox; die konkrete
Host-Konfiguration muss vor Ausführung nachgewiesen werden. Eine Dateisandbox
verhindert für sich genommen außerdem keine GitHub-Schreibaktion über das Netz.

Bis ein enger Ausführungspfad besteht, bereitet der Git Manager exakte Befehle und
Dateilisten vor; Main Manager führt nach den erforderlichen Freigaben die konkrete
Operation aus. Das ist ein kontrollierter Prozess, keine technisch isolierte neue
Agentenrolle. Ein **separater deterministischer Git-Executor** ist nur vorgeschlagen:
Allowlist für Repository, Worktree, Feature-Branch, Operation, Dateien und exakten SHA;
keine freie Shell, keine Git-Konfig-/Hook-Manipulation, keine Force-/Admin-Flags;
Lesetoken beim Planner und eingeschränkte Schreib-Credentials nur beim Executor.
Für Merge zusätzlich Einmalfreigabe mit PR/Head/Base sowie Revalidierung und
Branch Protection. Er ist nicht installiert und bietet hier noch keinen Schutz.

## Review, Push, PR und Merge

Main Manager überprüft Funktion, existierende/neue Tests, unnötigen Code und
Abhängigkeiten, Architektur, unerwünschte Dateien, Sicherheit und Datenschutz.
PrivyDesk: keine unautorisierten Dateizugriffe, unbestätigten destruktiven Aktionen,
unbeabsichtigten sensiblen Uploads oder unsichere Schlüsselablage.

Nach Review und lokalen Tests wird der konkrete Diff zur expliziten Dateiauswahl
freigegeben. Nach dem Commit prüfen, dass Commit/Tree ausschließlich diesen Diff
enthalten, SHA festhalten, erst dann Push. Git Manager erstellt PR nur wenn noch
keiner besteht, mit Base `dev`, und verknüpft Issue/PR durch gegenseitige Links.
`Refs #N` im PR, PR-URL im Issue; optional die Development-Verknüpfung prüfen.
[GitHub-Closing-Keywords](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue)
wirken nur bei Default-Branch-PRs; bei `dev` keine automatische Verknüpfung oder
Schließung durch `Closes` annehmen. Nach verifiziertem Merge autorisiert schließen,
sofern eine geprüfte Automation das nicht bereits korrekt erledigt hat.
Neue/übernommene/gepushte PRs sofort mit
T3 `link_pull_request` verknüpfen; fehlgeschlagene Verknüpfung melden.

Vor Merge: Issue vorhanden/verknüpft, lokale Tests bestanden, Code vom Main Manager
reviewt, tatsächliche finale CI erfolgreich, keine offenen Review-Blocker, kein
Konflikt und kein ungeprüfter Commit. Unbekannte/pending/skipped Checks nicht als
Erfolg behandeln. PR-Head und Base unmittelbar erneut lesen. Freigabe benennt beide
vollständigen SHAs. Base-Wechsel ebenfalls prüfen; bei relevanten Änderungen neu testen.
`gh pr merge --match-head-commit <approved-sha>` verhindert einen anderen Head;
es ersetzt keine serverseitigen Regeln für Änderungen an der Base. Bis Rulesets/CI
konfiguriert sind, bleibt der Merge gesperrt. Kein --admin, Force-Push oder --no-verify.

Wenn eine autorisierte PR-Aufgabe auf Checks/Reviews wartet, Main Manager nutzt
T3 `watch_pull_request` und beendet den Turn; Kinder beobachten nicht selbst.
Keine Poll-Schleifen oder gh --watch. Vor der abschließenden Rückgabe eines zuvor
beobachteten PRs ggf. `unwatch_pull_request` verwenden.

Nach bestätigtem Merge nach dev GitHub-Issue-Schließautomation überprüfen oder das Issue
autorisiert schließen; Project Done durch Automation oder gezielte Feldänderung.
Erst sauberen, nicht aktiven Worktree ohne unveröffentlichte Arbeit entfernen.
Kein erzwungenes Löschen, kein automatisches Entfernen fremder/unfertiger Branches.

## CI und Remote-Schutz: vorbereitet, nicht aktiviert

Es existieren keine Stack-Manifeste, Tests, Lint- oder Typecheck-Kommandos und keine
GitHub Actions. `.github/workflow-templates/privydesk-ci.yml.template` ist bewusst
inaktiv. Vor Aktivierung Stack/Runtime und Lockfile bestimmen, echte lokale Befehle
nachweisen, nötige Actions auf geprüfte SHAs pinnen und Jobs implementieren. Die
Vorlage selbst führt keine Anwendungstests aus und würde bei bloßem Umbenennen
mit einem expliziten Voraussetzungenfehler abbrechen.

Für main und dev: bestehende Branch Protection antwortet „Branch not protected“; Rulesets
sind leer, GitHub Auto-Merge ist aus. Adminrechte erlauben voraussichtlich Einrichtung,
aber Schutzänderungen und Merges wurden nicht getestet oder freigegeben. Nach gesonderter Zustimmung
für dev und spätere main-Releases PR-Pflicht, erforderliche CI-Checks, keine Force-Pushes/Löschungen und keine Bypässe
konfigurieren. Checknamen erst nach realem CI-Lauf festlegen. Bei Einzelentwicklerbetrieb
kann ein externer verpflichtender Reviewer zusätzliche Mitarbeit erfordern; Main-Manager-
Selbstreview ist keine unabhängige GitHub-Review-Freigabe. Nicht einfach unerfüllbare
Review-Regeln oder einen Admin-Bypass einrichten.

## Guthaben und Daten

Kein DeepSeek-Modellaufruf bei der Einrichtung; nur Paket-, Profil- und Provider-Metadaten.
Guthaben vor/nach echten Aufträgen im offiziellen Provider-Dashboard prüfen. Keine
feste technische Kostengrenze aus Promptregeln ableiten. Kontostandabfrage oder
Modellaufruf wird hier nicht ausgeführt. Kein Pro-Fallback, keine unbeschränkten Retries.
Keine API-Schlüssel im Projekt oder in Ausgaben, keine echten Nutzerdokumente für Tests,
keine globalen Änderungen oder Framework-Installationen. Externe KI bekommt nur die
für den Auftrag freigegebenen Dateien. Promptanweisungen ersetzen keine OS-/Netzwerkrechte.
