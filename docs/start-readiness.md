# Erster Entwicklungsdurchlauf: Vorbereitung und offene Freigaben

## Gesicherte Konfiguration

Die vorhandenen 23 Agenten-, Skill-, Vorlagen- und Workflow-Dateien wurden vollständig
inventarisiert und geprüft. Secret-Musterprüfung ohne Treffer (kein vollständiger
Beweis der Secretfreiheit), YAML-Frontmatter aller sieben Skills, TOML, lokale
Modellwerte und Codex-Startup-Konfiguration bestanden. Die mitgelieferten Superpowers-
Ressourcen stimmen anhand ihrer Git-Blob-Hashes mit dem festgehaltenen Upstream-Commit
überein. Das TypeScript-Beispiel und Shell-Hilfsskript sind Skill-Ressourcen, kein
PrivyDesk-Anwendungscode. Keine Anwendung existiert oder wurde verändert.

Sicherungsbranch: `chore/multi-agent-workflow`. Commit-Nachricht:
`chore: configure PrivyDesk multi-agent development workflow`.
main bleibt auf dem ursprünglichen Commit. Kein Push, PR oder Merge ist freigegeben.
Die spätere Aufnahme dieser vorbereitenden Dokumente gehört nur zur Konfigurations-
und Planungsarbeit und aktiviert keine vorgeschlagene Einstellung.

## Konkreter Project-Änderungsvorschlag – NICHT ausgeführt

User-Project [1](https://github.com/users/hakervlad/projects/1) für
[hakervlad/PrivyDesk](https://github.com/hakervlad/PrivyDesk) verwenden.
Es existiert, ist privat und offen, hat 19 Felder und keine Items; die Repo-Zuordnung
ist weiterhin leer. ADMIN-/Project-Lesezugriff ist bestätigt; kein Schreibtest.

Nach Nutzerfreigabe:

1. Mit `gh project link 1 --owner hakervlad --repo hakervlad/PrivyDesk` verknüpfen.
2. Vorhandenes Statusfeld weiterverwenden: Backlog, Ready, In progress, In review, Done.
   Die bestehenden passenden Schreibweisen bleiben erhalten. Keine Duplikate anlegen.
3. Ausschließlich Ready to Merge und Blocked als zusätzliche Optionen ergänzen.
   Die Datei `.github/project-status-change.proposed.json` enthält dafür einen noch
   nicht ausgeführten GraphQL-Mutationsvorschlag. Er erhält die vorhandenen Options-
   IDs, Farben und Beschreibungen. Vor Ausführung Optionen erneut lesen und bei jeder
   Abweichung den Vorschlag aktualisieren; kein veraltetes Komplettarray überschreiben.
4. Priorität P0/P1/P2 und vorhandene Labels weiterverwenden; keine neuen Felder oder
   Labels erforderlich. Issues erst nach separater Veröffentlichungserlaubnis anlegen.

Lesend tatsächlich gefundene, aktivierte Automationen:

- Auto-add sub-issues to project
- Auto-close issue
- Item closed
- Pull request merged

Die API stellt Namen/Enabled-Status bereit, aber im abgefragten Workflow-Typ keine
Trigger- oder Zielstatuskonfiguration. Diese muss vor Nutzung in der Project-Oberfläche
geprüft werden. Besonders Auto-close issue darf keinen verfrühten Abschluss auslösen:
Done immer erst nach bestätigtem Merge. Automationen nicht blind verändern oder
weitere parallele Statusautomationen anlegen. Repo-Verknüpfung bedeutet allein
keine automatische Aufnahme neuer Issues; bestehende passende Auto-add-Regel prüfen
oder Issues später autorisiert mit gh project item-add aufnehmen.

## Stack-Vergleich: Entscheidung ausstehend

Im Repository gibt es kein package.json, Cargo.toml oder anderes Stack-Manifest,
keine Anwendung und keine vorhandenen Test-/Build-Befehle.

| Kriterium | Tauri 2 + React/TypeScript + kleiner Rust-Kern | Electron + React/TypeScript + Node-Kern |
| --- | --- | --- |
| Einfache Entwicklung | Frontend vertraut; Rust und Linux-Buildpakete zusätzlich nötig | Einheitliches TypeScript und direkter Node-Zugang erleichtern den Einstieg |
| Lokales Dateisystem | Eng begrenzte Backend-Kommandos und Capability-Konfiguration; Pfadprüfung im Rust-Kern nötig | Node-Dateizugriffe im Main-Prozess; schmale geprüfte IPC-Schnittstelle nötig |
| Datenschutz | Lokal betreibbar; kein externer KI-Zwang | Ebenfalls lokal betreibbar; kein externer KI-Zwang |
| Sicherheit | Capabilities reduzieren Frontend-Zugriffe; eigener Rust-Code ist trotzdem privilegiert und muss Root/Identität prüfen | Renderer isolieren/sandboxen, Node-Integration dort deaktivieren; IPC-Sender und Nutzdaten prüfen |
| Testbarkeit | Rust-Dateikern und TypeScript-UI getrennt testbar; native UI separat prüfen | Dateikern und UI in TypeScript testbar; echte Prozess-/IPC-Grenze separat prüfen |
| Performance | System-WebView und nativer Kern; voraussichtlich geringerer Distributions-/Laufzeit-Overhead, hier nicht gemessen | Chromium-basierte Mehrprozessarchitektur; zusätzliche Runtime, hier nicht gemessen |
| Wartbarkeit | Zwei Sprachen, kleine klare Schnittstelle; System-WebViews pro Zielplattform berücksichtigen | Eine Sprache, aber Electron/Chromium/Node und Abhängigkeiten aktuell halten |
| Entwicklungskosten | Mehr Anfangsaufwand für Toolchain/Rust; eng begrenzten Dateikern klein halten | Wahrscheinlich schnellerer TS-Einstieg; laufende Runtime-/Sicherheitswartung einplanen |

Die Aufwand-/Performance-Einschätzung ist eine technische Abwägung, kein Benchmark
oder Geldversprechen. Beide Varianten können unsicher implementiert werden;
Framework-Capabilities ersetzen weder korrekte Pfad-/Handle-Prüfung noch OS-Rechte.
Tauri schützt insbesondere nicht vor unsicherem Rust-Code oder falschen Scope-Prüfungen.

**Empfehlung zur Zustimmung:** Tauri 2, React/TypeScript mit Vite für die UI und ein
kleiner Rust-Kern für Suche/Dateioperationen. Keine separate Serveranwendung,
Datenbank, Vektorindizierung oder zusätzliche KI-Integration in den ersten drei Issues.
Dies hält den privilegierten Dateizugriff zentral und die Oberfläche einfach.
Falls ein reines TypeScript-Projekt für Lernaufwand/Termindruck wichtiger ist,
ist Electron die begründete Alternative. Noch kein Stack beschlossen oder angelegt.

Auf diesem Ubuntu-System: Node 24.22.2 und npm 11.19.1 vorhanden; Rust/Cargo nicht
im PATH. libgtk-3-dev, build-essential und libssl-dev vorhanden; libwebkit2gtk-4.1-dev,
libayatana-appindicator3-dev, librsvg2-dev und libxdo-dev fehlen. Keine Pakete installiert.
Die Tauri-Voraussetzungen müssten nach Stackentscheidung gesondert eingerichtet werden;
dieser Auftrag autorisiert keine globalen Paket-/Systemänderungen.

Quellen: [Tauri Capabilities](https://v2.tauri.app/security/capabilities/),
[Tauri Voraussetzungen](https://v2.tauri.app/start/prerequisites/),
[Electron Sicherheitsregeln](https://www.electronjs.org/docs/latest/tutorial/security/),
[Electron Prozessmodell](https://www.electronjs.org/docs/latest/tutorial/process-model).

## CI und main-Schutz

Die inaktive Vorlage unter `.github/workflow-templates/privydesk-ci.yml.template`
ist geprüft und bleibt inaktiv. Sie besitzt contents:read und einen explizit
fehlschlagenden Voraussetzungen-Job; dieser ist kein Ersatz für Anwendungstests.
Ohne genehmigten Stack und tatsächlich vorhandene Befehle wird kein grüner
GitHub-Actions-Workflow erfunden oder aktiviert.

Issue „Technische Projektgrundstruktur“ muss nach Stackfreigabe echte Tests,
Lint-/Typecheck-/Build-Einstiege und eine Lockdatei schaffen. Erst nachdem diese
Befehle lokal existieren und funktionieren, kommen dieselben Befehle in CI.
Keine leeren Tests oder als bestanden gewerteten fehlenden Checks. Native Linux-
Buildabhängigkeiten und genehmigte Actions-Versionen gehören in diesen Aufbau.

main hat aktuell weder Branch Protection noch Rulesets; Actions listet 0 Workflows.
Für das öffentliche Repo besteht ADMIN-Zugriff. Vorschlag für gesonderte Zustimmung:
PR-Pflicht, erfolgreiche konkret benannte CI-Checks, Aktualität zur Base, kein
Force-Push/Löschen, keine Bypass-Liste und keine Admin-Umgehung. Checknamen erst
nach echtem CI-Lauf festlegen. Bei Einzelentwicklerbetrieb nicht ungeprüft eine
unerfüllbare Fremdreview-Anzahl konfigurieren. Main-Manager-QA und ausdrückliche
Nutzerfreigabe des finalen Head-SHA bleiben zusätzliche Prozessvoraussetzungen.

Bootstrap-Zirkelschluss beachten: main enthält noch keine Konfiguration/CI. Ein
Konfigurations-PR darf ohne tatsächliche passende Prüfungen nicht als grün gelten.
Vor dem ersten Merge muss ein realer, für seinen Inhalt passender CI-Lauf eingerichtet
und die Schutzkonfiguration autorisiert sein; alternativ den Workflow zunächst nur
auf dem lokalen Bootstrap-Commit testen. Kein stiller Sondermerge.

## T3 und gemeinsamer Worktree

Codex CLI 0.166.0, DSH 0.2.1-rc.5, gh 2.45.0 und Git 2.43.0 sind vorhanden.
T3 meldet Main Manager Astra/high, Codex Luna/low verfügbar, Cross-Provider-Delegation
und den aktivierten/delegierbaren Provider acpRegistry_deepseek. Dessen lokaler
Launch ist dsh --profile acp; Profil sowie ACP-/Basispaket sind vorhanden.
Keine DSH-Sitzung oder Modellanfrage gestartet; API-Schlüssel, Modellantwort und
Guthaben wurden nicht geprüft.

Der Git Manager wurde erfolgreich als Luna/low mit isolation=shared gestartet.
Seine tatsächlichen Shell-Ausgaben bestätigen denselben Projektroot, Branch
chore/multi-agent-workflow und Head 37c8885ed0aa945b79d94ab147670007fe3298e8
wie beim Main Manager. Er hat 0 Issues gefunden und drei Entwürfe geliefert; keine
Dateien oder GitHub-Ressourcen geändert. Main Manager hat die Befunde unabhängig
lesend gegengeprüft; Entwürfe stehen in docs/issue-drafts.md.

Auffällig: T3-Thread-Metadaten des Kindes melden noch branch=main und worktreePath=null,
obwohl die echten Git-Ausgaben den Konfigurationsbranch zeigen. Für den gemeinsamen
Root-Checkout ist deshalb der tatsächliche cwd-/Git-Nachweis entscheidend, nicht
der Thread-Titel oder ein veraltetes Branchfeld. Das prüft die Codex-Delegation,
nicht die DeepSeek-Laufzeit oder einen neuen linked Issue-Worktree. Bei dessen
Anlage muss die konkrete Bindung erneut geprüft werden. Kein technisch erzwungener
read-only-Schutz wird behauptet; die Kindlaufzeit war full-access mit Leseauftrag.

Für das erste echte Issue:

1. Nach Issue-Freigabe einen eindeutigen feat/<Nummer>-<slug>-Worktree über T3 organisieren.
   Als Basis einen Commit verwenden, der die gesicherte Workflow-Konfiguration enthält.
2. Main Manager muss vor TDD tatsächlich in diesem Issue-Worktree gebunden sein.
   Für aktuelle Sitzung t3_worktree_handoff mit genehmigter Basis und runSetupScript:false;
   der Aufruf beendet den Turn und setzt mit einem präzisen continuationPrompt fort.
3. DeepSeek anschließend nur vom gebundenen Issue-Thread mittels delegate_task,
   providerInstanceId=acpRegistry_deepseek, isolation=shared starten. Der Auftrag enthält
   den exakten Worktree, Branch und Basis-Head. Noch vor Änderungen muss der Developer
   dieselben Werte bestätigen; Abweichung stoppt den Auftrag.
4. Falls eine bestehende Worktree-Bindung stattdessen einen eigenen Thread benötigt:
   nach dem entsprechenden Nutzerauftrag t3_thread_launch mit
   workspaceStrategy={type:"existing_worktree",worktreePath:<absoluter-Issue-Pfad>}.
   Keine Worktree-Neuanlage, keine bloße cd-Anweisung als Bindungsnachweis.
5. Falls der Provider einen falschen Checkout verwendet: Aufgabe abbrechen, keine
   Dateien kopieren/parallel entwickeln; alternative explizite Thread-Bindung prüfen.
   Ohne nachgewiesene gemeinsame Bindung kein Implementierungsstart.

Git-Worktree-CLI und T3-Schemas für shared/existing_worktree sind geprüft. Eine
Modellanfrage wäre zum tatsächlichen DeepSeek-Bindungsnachweis nötig und ist hier
verboten. Die verbleibende Grenze wird nicht als bestandener End-to-End-Test dargestellt.

## Nötige Entscheidungen

- Stack: Tauri-Vorschlag bestätigen oder Electron wählen; keine Implementierung davor.
- GitHub: Repo mit Project 1 verknüpfen und zwei Optionen ergänzen; bestehende Felder erhalten.
- Issues: die drei fertigen Entwürfe gesondert zur Veröffentlichung freigeben.
- Push: den konkreten geprüften Konfigurationscommit ausdrücklich freigeben.
- main-Schutz: konkretes Ruleset nach tatsächlichem CI-Checknamen freigeben.
- DeepSeek: erst bei einem echten freigegebenen Implementierungsauftrag und geprüftem Guthaben.
