# Vorbereitete Entwicklungsissues – noch nicht veröffentlicht

Der Git Manager (gpt-6-luna, low) hat alle offenen und geschlossenen Repository-Issues
lesend geprüft: 0 vorhanden, keine Duplikate. Main Manager hat die Entwürfe geprüft
und die Kriterien für echte CI sowie Pfad-/Bestätigungsgrenzen konkretisiert.
Die Nummern 1–3 sind Entwurfsnummern, keine zugewiesenen GitHub-Issue-Nummern.
Nach Veröffentlichung werden diese Entwürfe durch die echten Issue-Links ersetzt;
keine parallele Statuspflege in dieser Datei.
Nutzerfreigabe für Veröffentlichung und Project-Aufnahme liegt vor; die zusätzliche
Bedingung „vier Automationen im Detail prüfen“ ist noch offen. Bis dahin nicht publizieren.
Alle drei Feature-Branches starten von dev; PR-Ziel dev. KI-Konfiguration bleibt in Ops.
Anfangsstatus Backlog;
Ready erst nach erfüllten Voraussetzungen. Prioritäten sind Vorschläge.

Git-Manager-Lauf: <t3-thread://v2/578517d3-d173-40e9-bab5-17221ad170fd/thread:delegated-task:command%3Amcp%3Ab6a9b051-aec9-4cd4-9556-a1ded6efc470%3Adelegate-task%3Afb063d2c-11fc-4d43-822f-c1d237cf2548>
Historischer Vorbereitungsnachweis vor Branch-Trennung, kein aktueller Ziel-Checkout:
Beobachteter gemeinsamer Checkout: Projektroot; Branch chore/multi-agent-workflow;
Head während der Prüfung 37c8885ed0aa945b79d94ab147670007fe3298e8. Keine Schreibaktionen.

## Entwurf 1: Technische Projektgrundstruktur

**Titel:** PrivyDesk – Desktop-App-Grundstruktur erstellen

**Beschreibung**

Den bestätigten Stack Tauri 2 + React + TypeScript + Rust als minimale Desktop-App
einrichten. React/TypeScript für die Oberfläche, kleiner Rust-Kern über Tauri;
keine Serveranwendung, Datenbank, Dateiindizierung oder KI-Integration.

**Priorität:** P1 (vorhandenes Project-Feld).

**Abhängigkeiten:** Stack freigegeben. Vor Veröffentlichung die vier Project-
Automationsbedingungen prüfen. Vor Implementierung vorhandenes Rust/Cargo über ~/.cargo/bin verfügbar machen und
fehlende Linux-Buildpakete nach Installationsfreigabe bereitstellen, echte Issue-Nummer erhalten,
Zuständigkeit abstimmen, Assignee/Status setzen und zurücklesen. Kein Start bei
unklarer Übernahme; auch Zuständigkeiten des Freundes und seiner Agenten respektieren.

**Akzeptanzkriterien**

- [ ] Tauri 2 mit Rust-Backend ist eingerichtet.
- [ ] React und TypeScript sind eingerichtet.
- [ ] Eine minimalistische Desktop-Oberfläche startet ohne Laufzeitfehler.
- [ ] Lokaler Entwicklungsstart und Build funktionieren mit dokumentierten, tatsächlich ausgeführten Befehlen.
- [ ] Grundlegende sinnvolle Tests sind vorhanden; die Suite prüft sichtbares UI-Verhalten statt nur Dateiexistenz und läuft erfolgreich.
- [ ] Keine unnötigen Abhängigkeiten, Abstraktionen oder Zusatzfunktionen.
- [ ] Der vollständige App-Tree enthält keine `.agents/`, `.dsh/`, `.codex/`, `AGENTS.md` oder reine KI-Orchestrierungsdokumentation; auch dev bleibt davon frei.
- [ ] Reale Test-, Typecheck-/Lint- und Build-Einstiege werden passend zum tatsächlichen Projekt dokumentiert und lokal geprüft; keine erfundenen Befehle oder leeren grünen Suites.
- [ ] Minimale CI führt die vorhandenen relevanten Prüfungen bei PRs nach dev aus und ist am finalen PR-Head erfolgreich.

**Testanforderungen:** Main schreibt nach Bereitstellung der minimal erforderlichen
Testinfrastruktur einen beobachtbar fehlschlagenden Test für das vereinbarte UI-
Startverhalten vor dessen Implementierung. Developer implementiert nur den Auftrag.
Lokalen Desktop-Start zusätzlich manuell prüfen; Unit-Tests ersetzen diesen Nachweis
nicht. Build und relevante Prüfungen lokal ausführen; Testfehler blockieren Freigabe.
Kein Produktionscode nur zur Erfüllung eines selbstreferenziellen Strukturtests.

**Sicherheitsanforderungen:** Nur synthetische/temporäre Testdaten. Keine Nutzerdokumente,
Schlüssel oder Zugangsdaten. Keine Dateisystem-, Shell- oder Netzwerk-Plugins ohne
Bedarf; keine Dateisuche/Dateioperationen oder KI-Uploads in diesem Issue ergänzen.
Tauri-Capabilities auf den vereinbarten UI-Umfang beschränken und prüfen.

**Zuständigkeit:** Noch keine Übernahme vereinbart. Erst nach Veröffentlichung die
live Assignees und Status prüfen; bei Konkurrenz nicht übernehmen.

**Branch:** `feat/<ECHTE-ISSUE-NUMMER>-desktop-foundation`, von geprüftem `dev`.

**Worktree:** `/home/vladimir/.t3/worktrees/PrivyDesk/feat/<ECHTE-ISSUE-NUMMER>-desktop-foundation`
(nur lokaler Planungspfad, nicht mit privaten Pfaden veröffentlichen).

**PR:** nach `dev`, später echte URL ergänzen. `Refs #N` plus PR-Link im Issue;
kein automatisches Schließen durch Keywords bei Nicht-Default dev voraussetzen.

## Entwurf 2: Lokale Dateisuche

**Titel:** Lokale Dateisuche auf einen freigegebenen Testordner begrenzen

**Beschreibung**

Eine lokale Suche soll ausschließlich innerhalb eines vom Nutzer ausdrücklich freigegebenen Root-Verzeichnisses arbeiten. Umfang und Suchverhalten sind vor Implementierung mit dem Main Manager festzulegen. Keine KI-Uploads, Dateiinhaltsindizierung oder weiteren Suchfunktionen ergänzen.

**Priorität:** P1

**Abhängigkeiten:** Entwurf 1 und dessen abgeschlossene Grundstruktur; reale Issue-Nummer nach Veröffentlichung verknüpfen.

**Akzeptanzkriterien**

- [ ] Die Suche verlangt einen ausdrücklich freigegebenen Root und liefert Treffer nur innerhalb dieses Verzeichnisses.
- [ ] Pfade mit `..`, Symlinks und Permission-Fehler können den Suchlauf nicht aus dem freigegebenen Root herausführen; betroffene Pfade werden sicher abgewiesen oder übersprungen.
- [ ] Bei fehlendem Zugriff auf den Root oder einen Pfad entstehen keine externen Zugriffe oder Dateiänderungen.
- [ ] Ein vereinbarter Dateinamen-Suchbegriff findet passende Dateien im Testordner; keine Treffer, leere Ordner und ungültige Eingaben liefern definierte Ergebnisse.
- [ ] Die Suche verändert keine Datei und sendet weder Dateinamen noch Inhalte an externe Dienste.

**Tests:** Ausschließlich synthetische oder temporäre Dateien und Verzeichnisse; insbesondere Fälle für `..`, Symlinks und verweigerte Berechtigungen abdecken. Testbefehle erst anhand der vorhandenen Infrastruktur festlegen.

**Sicherheit:** Kein Zugriff außerhalb des freigegebenen Roots, auch nicht durch Symlinks oder Fehlerfälle. Keine KI-Uploads, Dateiinhaltsindizierung oder Zusatzfunktionen.

**Branch:** `feat/<issue-number>-approved-folder-search`

**PR:** `<PR-Link, später ergänzen>`

## Entwurf 3: Sichere Dateioperationen

**Titel:** Lokale Dateioperationen mit ausdrücklicher Einzelbestätigung absichern

**Beschreibung**

Dateiänderungen dürfen nur nach konkreter Bestätigung des Nutzers für die jeweilige Operation stattfinden. Welche ersten Operationen unterstützt werden, ist vor Implementierung ausdrücklich als Umfang zu bestätigen; dieser Entwurf legt keine zusätzlichen Operationen fest.

**Priorität:** P1

**Abhängigkeiten:** Entwürfe 1 und 2; reale Issue-Nummern nach Veröffentlichung verknüpfen und Voraussetzungen erfüllen.

**Akzeptanzkriterien**

- [ ] Vor jeder Dateiänderung werden Ziel und konkrete Operation angezeigt und einzeln bestätigt.
- [ ] Abbruch oder fehlende Bestätigung lässt Dateien unverändert.
- [ ] Unmittelbar vor der Ausführung werden Root-Scope und Dateiidentität erneut geprüft; Änderungen oder Scope-Verletzungen verhindern die Operation.
- [ ] Ein Pfad-/Symlink-Austausch zwischen Prüfung und Ausführung kann keine Operation außerhalb des freigegebenen Roots bewirken.
- [ ] Bestätigungen gelten genau einmal für die angezeigte Operation und Dateiidentität; Änderungen machen sie ungültig.
- [ ] Bestehende Dateien werden nicht still überschrieben; Konflikte stoppen die Operation und werden angezeigt.
- [ ] Die vereinbarten ersten Operationen sind mit ausschließlich synthetischen oder temporären Testdateien überprüfbar.

**Tests:** Nur synthetische oder temporäre Dateien; Abbruch, geänderte Dateiidentität, Scope-Verletzung und Überschreibungskonflikt prüfen. Keine echten Nutzerdokumente.

**Sicherheit:** Keine Mutation ohne konkrete Einzelbestätigung, erneute Scope-/Identitätsprüfung und sichtbare Konfliktbehandlung. Keine stillen Überschreibungen, KI-Uploads oder Inhaltsindizierung.

**Branch:** `feat/<issue-number>-confirmed-file-operations`

**PR:** `<PR-Link, später ergänzen>`
