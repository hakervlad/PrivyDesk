# Entwicklungsumgebung: Branch-Trennung und Startvoraussetzungen

Prüfstand: 2026-11-05. Einrichtungsbericht, keine parallele Issue-Statusverwaltung.
Verbindlicher Ablauf: [development-workflow-v2.md](development-workflow-v2.md) und
[AGENTS.md](../AGENTS.md). Der frühere [Preflight](workflow-v2-preflight.md) ist historisch.

## Branches und Worktrees

| Branch | Zweck und geprüfter Ausgangsstand | Checkout |
| --- | --- | --- |
| `main` | Stabile Releases; unverändert `f94bc6cf7646a8c4485282f9dfb7c7119f6dab5b`, nur README | Kein eigener Checkout |
| `dev` | App-Integration; lokaler Tracking-Branch von vorhandenem `origin/dev`, gleicher README-Stand | `/home/vladimir/Dokumente/uni/sem7/Frintier Systems/PrivyDesk-dev` |
| `ops/agent-workflow` | KI-Konfiguration, Basis `27730d1881ab934d35609ee9f4789f1f6a031baa`; lokale Branch-Trennung folgt darauf | `/home/vladimir/.t3/worktrees/PrivyDesk/ops-agent-workflow` |
| `chore/multi-agent-workflow` | Unveränderte Sicherung `27730d1881ab934d35609ee9f4789f1f6a031baa`, bereits früher autorisiert gepusht | `/home/vladimir/Dokumente/uni/sem7/Frintier Systems/PrivyDesk` |

Remote-Branches: `origin/main`, `origin/dev`, `origin/chore/multi-agent-workflow`,
`origin/ops/agent-workflow`.
Ops wurde zum Start der Entwicklungsphase mit dem geprüften Commit
`73c34d9348ed8fae60dae8432b7e95f32b96d692` autorisiert gepusht und remote zurückgelesen.
Die neue Übernahmeregel, Stackfreigabe und Startplanung werden danach separat
als geprüfter Folgecommit ausschließlich auf demselben Ops-Branch gesichert/gepusht.
Kein Merge; main/dev unverändert.
Die README-/Anwendungstrees von main/dev werden nicht verändert. Alle sieben Skills,
Agenten-TOML, die gesamte `.codex/`, `.dsh/`, `.agents/`, AGENTS.md und reine
KI-Orchestrierungsdocs bleiben im Ops-Zweig; die historische Chore-Sicherung bleibt
bewusst erhalten. Keine Konfigurationsdateien in den Dev-Checkout kopiert.
Die Ops-Historie darf nicht nach dev/main gemergt oder cherry-gepickt werden.

## Rollen, Skills und technische Nachweise

- Main Manager: Codex Astra/high, dauerhaft im Ops-Worktree.
- Git Manager: Codex Luna/low; unveränderte Planerrolle und Sandbox-Einschränkungsabsicht.
- Developer: DeepSeek Harness über ACP, nur spätere Implementierung im Feature-Worktree.
- Skills: test-driven-development, verification-before-completion, systematic-debugging,
  privacy-review, github-project-management, git-worktree-pr und minimal-code.
  Kein neuer Skill und kein zusätzliches Framework.

Der echte T3-Handoff ist erfolgt. Nach Fortsetzung stimmen Shell-pwd, Git-Top-Level,
Branch ops/agent-workflow und T3-worktreePath überein. Start-Head war 27730d1;
T3-Auswahl meldet Astra mit reasoningEffort high. Der neue Dev-Worktree ist ein
Git-Checkout, noch kein daran gebundener T3-Thread.

Codex CLI 0.166.0, DSH 0.2.1-rc.5, gh 2.45.0 und Git 2.43.0 wurden zuvor lokal
geprüft. ACP-Provider acpRegistry_deepseek ist registriert/delegierbar; DSH-Profil
und Paket sind vorhanden. Kein DSH-Prozess, Modelltest oder Endpoint-/Guthabentest
wurde für die Einrichtung gestartet. Registrierung ist kein End-to-End-Nachweis.

Historisch wurde Git Manager als Luna/low lesend im Root-Checkout getestet und
lieferte die drei Entwürfe. Damals waren T3-Branch-Metadaten veraltet; tatsächliche
Git-Ausgaben sind daher immer zusätzlich erforderlich. Dieser Nachweis testet
weder DeepSeek noch die neue Feature-Worktree-Bindung.

Die Git-Manager-TOML behält read-only; die aktuelle Elternlaufzeit ist full-access.
Technischer Schreibschutz des Kindes ist nicht nachgewiesen. Ohne nachgewiesene
Isolation bereitet Git Manager Befehle vor, Main führt konkret freigegebene Aktionen
aus. Lokale Rechte, Token-Rechte, Main-Freigabe und Nutzerzustimmung sind getrennt.

## Project und drei unveröffentlichte Entwürfe

[Project 1](https://github.com/users/hakervlad/projects/1) ist mit
[hakervlad/PrivyDesk](https://github.com/hakervlad/PrivyDesk) verbunden.
Bestehendes Statusfeld: Backlog, Ready, In progress, In review, Ready to Merge,
Done, Blocked. Priority P0/P1/P2 bleibt erhalten; keine doppelten Felder.
Die bereits autorisierte Verknüpfung und Statusergänzung wurden früher durchgeführt
und zurückgelesen. Der alte Mutationsvorschlag ist nur noch als ausdrücklich
nicht erneut ausführbarer historischer Nachweis unter
[history/project-status-change.applied.json](history/project-status-change.applied.json)
archiviert. Er ist kein offener Änderungsauftrag.

Zuletzt 0 Issues und 0 Project-Items, keine Duplikate. Vier Automationen sind aktiv:
Auto-add sub-issues to project, Auto-close issue, Item closed, Pull request merged.
Die API liefert deren Namen/Enabled-Zustand, aber keine Trigger-/Zielstatusdetails.
Der bisherige Browserzugriff auf das private Project war nicht angemeldet; deshalb
sind die Detailbedingungen weiterhin **ungeprüft**. Insbesondere prüfen, welcher
Status Auto-close auslöst, wie Item closed den Status setzt, ob PR merged auch
PRs nach dev erfasst und welche Sub-Issues automatisch aufgenommen werden.
Kein vorzeitiges Done/Schließen oder Überschreiben von Blocked/Review zulassen.

[issue-drafts.md](issue-drafts.md) enthält die drei geprüften Entwürfe:
Projektgrundstruktur, lokale Dateisuche, bestätigte Dateioperationen. Ihre
Veröffentlichung war genehmigt, bleibt aber an die noch fehlende Automationsprüfung
gebunden. Keine Issues veröffentlicht, keine Automationen verändert. Repository-
Zuordnung ist kein Beweis automatischer Issue-Aufnahme; Aufnahme später prüfen.

## Stack bestätigt: Tauri 2 + React + TypeScript + Rust

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

**Bestätigter Stack:** Tauri 2, React/TypeScript mit Vite für die UI und ein
kleiner Rust-Kern für Suche/Dateioperationen. Keine separate Serveranwendung,
Datenbank, Vektorindizierung oder zusätzliche KI-Integration in den ersten drei Issues.
Dies hält den privilegierten Dateizugriff zentral und die Oberfläche einfach.
Der obige Vergleich dokumentiert die vorausgegangene Abwägung. Der Nutzer hat
Tauri 2 + React + TypeScript + Rust bestätigt; noch keine Anwendung angelegt.

Auf diesem Ubuntu-System: Node 24.22.2 und npm 11.19.1 vorhanden; Rust 1.95.0/Cargo 1.95.0 über ~/.cargo/bin vorhanden, aber nicht im aktuellen
PATH (der frühere PATH-Befund bedeutete nicht, dass Rust installiert werden muss). libgtk-3-dev, build-essential und libssl-dev vorhanden; libwebkit2gtk-4.1-dev,
libayatana-appindicator3-dev, librsvg2-dev und libxdo-dev fehlen. Keine Pakete installiert.
Die Tauri-Voraussetzungen müssen nach gesonderter Installationsfreigabe eingerichtet werden;
dieser Auftrag autorisiert keine globalen Paket-/Systemänderungen.

Quellen: [Tauri Capabilities](https://v2.tauri.app/security/capabilities/),
[Tauri Voraussetzungen](https://v2.tauri.app/start/prerequisites/),
[Electron Sicherheitsregeln](https://www.electronjs.org/docs/latest/tutorial/security/),
[Electron Prozessmodell](https://www.electronjs.org/docs/latest/tutorial/process-model).

## CI und Schutz von dev/main

Die inaktive `.github/workflow-templates/privydesk-ci.yml.template` liegt nur in Ops
und zielt künftig auf Pull Requests nach dev. Sie hat contents:read und einen
absichtlich fehlschlagenden Voraussetzungen-Job, keine behaupteten Anwendungstests.
Keine aktive CI, weil reale Test-/Lint-/Typecheck-/Build-Befehle fehlen. Die Vorlage
später selektiv im Grundstruktur-Feature umsetzen; nicht Ops nach dev mergen.
App-Vorlagen können ebenfalls einzeln geprüft übernommen werden; die jetzige
PR-Vorlage enthält Rollenregeln und muss vor Übernahme von KI-spezifischen Anweisungen
bereinigt werden. Ops-Vorlagen werden im Default-Branch nicht automatisch angeboten.

main/dev sind laut Vorprüfung ungeschützt, Rulesets leer. Nach tatsächlichen CI-Läufen
Checknamen festlegen und erst mit gesonderter Nutzerzustimmung PR-/CI-Pflicht,
Aktualität zur Base und Schutz gegen Force-Push/Löschen/Bypass einrichten. Der
Feature-Workflow bleibt vor dem Merge blockiert, bis CI und vereinbarter Schutz
vorhanden sind. Kein Schutzmechanismus wurde hier aktiviert.

## Cross-Worktree-Ablauf für den ersten echten Durchlauf

1. Automationsdetails prüfen; anschließend die bereits genehmigten Entwürfe ohne
   Duplikate veröffentlichen und zu Backlog hinzufügen. Stackentscheidung ist bestätigt.
   Vor Übernahme Assignee/Status mit allen Beteiligten abstimmen, setzen und zurücklesen.
2. Für das freigegebene Issue den aktuellen dev-Stand prüfen und einen eindeutigen
   Feature-Worktree organisieren. Main bleibt in Ops und schreibt/prüft die Tests
   über explizite absolute Feature-Pfade und Tool-workdir, zeitlich vor Developer.
3. Vor dem echten kostenpflichtigen Auftrag Guthaben und konkrete ACP-Modellwahl
   prüfen. Den gewünschten Developer-Thread per t3_thread_launch ausdrücklich mit
   existing_worktree und absolutem Feature-Pfad binden. Kein shared-Delegate aus Ops!
4. Vollständige kurze Minimal-Code-Regeln aus development-workflow-v2.md sowie
   Issue, Aufgabe, Pfad, Branch, Basis-/Start-SHA, relevante Dateien, Kriterien und
   Tests im Auftrag mitsenden. Kein Repo-Vollkontext, keine getrackten Agentenfiles.
5. Developer bestätigt echte cwd/Top-Level/Branch/Head vor Änderungen. Main liest
   t3_thread_read (ggf. activity), bei unmittelbarem Bedarf t3_thread_wait, und prüft
   den Feature-Diff selbst. Maximal ein Entwickler, zwei Korrekturrunden; kein Polling.
6. Lokale QA, konkrete Commit-/Push-Freigabe, PR nach dev, echte CI und erneute Prüfung
   der vollständigen Head-/Base-SHAs. Finaler Merge benötigt zusätzliche Nutzerbestätigung.
   Nach verifiziertem Merge das Issue kontrolliert schließen und Project aktualisieren.

T3-Schema existing_worktree und der echte Ops-Handoff sind geprüft. Ein gebundener
DeepSeek-Feature-Thread samt Ergebnisabruf ist nur vorbereitet, **nicht ausgeführt**.
Branch-Trennung und Promptregeln bieten keine technische Isolation des Dateisystems.
Bei falschem Checkout stoppen; keinen Worktree im Prompt umbiegen oder Konfiguration
in dev kopieren. Siehe development-workflow-v2.md für Abbruch und Übergaben.

## Verifikation und noch fehlende Freigaben

Bestanden: zwei TOML-Dateien mit unveränderten Modellen/Reasoning-Werten,
YAML-Frontmatter der sieben Skills, inaktive CI-Vorlage mit Ziel dev, Codex strict
startup (Exit 0, gültiges JSON, kein Modellstart) und git diff --check. Die vollständigen
getrackten main/dev-Trees entsprechen exakt dem ursprünglichen README-Tree;
Original- und Dev-Worktree sind sauber. Bekannte Secret-Muster ergaben keine Treffer
(kein vollständiger Beweis der Secretfreiheit). Der archivierte Project-Request
enthält den ursprünglichen Inhalt unverändert als historisches Unterobjekt.
Diese Konfigurationsprüfungen ersetzen keine App-Tests
und keinen DeepSeek-End-to-End-Lauf. Commit-SHA der geprüften Ops-Änderung wird im
Abschlussbericht genannt, ohne selbstreferentiellen SHA in diesem Dokument.

Offen: Automationsdetails; gesondert erlaubte Toolchain-
Einrichtung und echte Test-/Build-Befehle; CI-Lauf und genehmigter dev/main-Schutz;
erster ausdrücklich freigegebener DeepSeek-Auftrag mit geprüftem Guthaben. Ops-Push ist für diesen Auftrag freigegeben und für den geprüften Ausgangsstand erfolgt. Keine Installation, Implementierung oder Remote-
Mutation ist aus dieser Dokumentation automatisch autorisiert.


## Erste Implementierung: Plan und aktuelle Sperren

Git Manager Luna/low hat erneut lesend geprüft: 0 offene/geschlossene Issues,
dev und origin/dev auf f94bc6cf7646a8c4485282f9dfb7c7119f6dab5b, kein Feature-Branch.
Er hat Entwurf und Branch-/Worktree-Befehle vorbereitet; wegen unbestätigtem Sandbox-
Schreibschutz führt Main später die konkret genehmigten lokalen Schritte aus.
Noch kein Feature-Branch oder Worktree: Ohne veröffentlichtes Issue gibt es keine
echte Nummer; keine vorläufige Nummer oder Eigentumsannahme verwenden.

Nach erfüllten Gates: vorhandene Branches/Worktrees und aktuellen dev-Head erneut
prüfen; den eindeutigen lokalen Branch `feat/<ECHTE-ISSUE-NUMMER>-desktop-foundation`
mit eigenem Worktree von genau diesem geprüften dev-SHA anlegen. Nicht den heutigen
SHA ungeprüft wiederverwenden. Niemals von Ops abzweigen. Issue-Aufnahme, Zuständigkeit,
Status und Branch-Zuordnung zurücklesen; erst dann Implementierungsauftrag.

Implementierungsplan für das genehmigte Grundstruktur-Issue:

1. Main legt im Feature-Worktree die minimale passende Testinfrastruktur und einen
   sichtbaren fehlschlagenden UI-Verhaltenstest an; falls vor Projektgrundgerüst nicht
   ausführbar, erst Test-Harness/Manifeste minimal bereitstellen, dann Red nachweisen.
2. Developer erhält einen kleinen Grundstruktur-Auftrag: Tauri2, React/TS, Rust,
   minimale Oberfläche; kein Dateizugriff/Upload, keine unnötigen Plugins. Kein Start
   während dieser Vorbereitung. Main bleibt in Ops, benutzt explizite Feature-Pfade.
3. Vollständige Minimal-Code-Regeln und aktueller Issue-/Übernahmenachweis werden
   im Prompt mitgegeben, keine Ops-Konfiguration in getracktes App-Tree kopiert.
   T3 existing_worktree bindet den Developer vor Start; cwd/Branch/Head bestätigen.
4. Main prüft tatsächlichen Diff, UI-Test, Typechecking, relevante Rust-Prüfungen,
   Build und echten Desktop-Start. Befehle erst vom entstandenen Projekt ableiten.
   Dann minimale PR-CI nach dev mit genau diesen Befehlen einrichten und verifizieren.
5. Höchstens zwei gezielte Korrekturrunden, nur ein Developer gleichzeitig. Kein
   Commit/Push des Features ohne QA-Freigabe und kein Merge ohne finale Nutzerzustimmung.

Aktuelle Toolchain-Prüfung: Node 24.22.2, npm 11.19.1, C/C++-Toolchain/pkg-config,
GTK3-Dev, SSL-Dev, curl, wget, file vorhanden. Rust/Cargo 1.95.0 liegen unter
~/.cargo/bin; rustup meldet stable als aktives Default-Toolchain. Nur der aktuelle
PATH enthält das Verzeichnis nicht. Für spätere Befehle ~/.cargo/bin pro Prozess
zum PATH hinzufügen oder absolute Binärpfade verwenden; keine globale Konfiguration
geändert. Kein Rust-Download nötig, keine Installation ausgeführt.
Fehlende Systempakete: libwebkit2gtk-4.1-dev, libxdo-dev,
libayatana-appindicator3-dev, librsvg2-dev. Offizielle Referenz:
https://v2.tauri.app/start/prerequisites/ . Keine Installation ausgeführt.

Project-Workflow-API erneut introspektiert: ProjectV2Workflow liefert ausschließlich
Metadaten (name, enabled, id/number, Zeitstempel, project), keine Bedingungen/Aktionen.
Daher muss der Nutzer angemeldet unter Project 1 → Menü → Workflows Folgendes prüfen:

- Auto-close issue: Welcher Status/Filter schließt Issues? Darf nicht vor bestätigtem
  Merge und erfüllten Kriterien auslösen; besonders Done-Übergänge kontrollieren.
- Item closed: Welcher Zielstatus wird gesetzt? Wechselwirkungen mit Auto-close und
  manuellem Schließen prüfen; nicht irrtümlich als verifizierten Abschluss werten.
- Pull request merged: Welche Items/Repos/Zielbranches erfasst die Regel, welchen Status
  setzt sie? Ein PR-Merge darf nicht ungeprüft das zugehörige Issue abschließen.
- Auto-add sub-issues to project: Welche Sub-Issues/Repos werden aufgenommen und mit
  welchem Initialstatus? Keine konkurrierende Übernahme/Statusüberschreibung zulassen.

Diese Bedingungen wurden nicht als sicher bestätigt. Keine Automationen geändert,
keine Issues publiziert, kein Assignee/Status gesetzt. Rückmeldung der konkreten
Bedingungen ermöglicht die nächste Prüfung; Namen/Enabled allein reichen nicht.
