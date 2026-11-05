# Vorbereitete Entwicklungsissues – noch nicht veröffentlicht

Der Git Manager (gpt-6-luna, low) hat alle offenen und geschlossenen Repository-Issues
lesend geprüft: 0 vorhanden, keine Duplikate. Main Manager hat die Entwürfe geprüft
und die Kriterien für echte CI sowie Pfad-/Bestätigungsgrenzen konkretisiert.
Die Nummern 1–3 sind Entwurfsnummern, keine zugewiesenen GitHub-Issue-Nummern.
Nach Veröffentlichung werden diese Entwürfe durch die echten Issue-Links ersetzt;
keine parallele Statuspflege in dieser Datei.
Veröffentlichung und Project-Aufnahme brauchen Nutzerfreigabe. Anfangsstatus Backlog;
Ready erst nach erfüllten Voraussetzungen. Prioritäten sind Vorschläge.

Git-Manager-Lauf: <t3-thread://v2/578517d3-d173-40e9-bab5-17221ad170fd/thread:delegated-task:command%3Amcp%3Ab6a9b051-aec9-4cd4-9556-a1ded6efc470%3Adelegate-task%3Afb063d2c-11fc-4d43-822f-c1d237cf2548>
Beobachteter gemeinsamer Checkout: Projektroot; Branch chore/multi-agent-workflow;
Head während der Prüfung 37c8885ed0aa945b79d94ab147670007fe3298e8. Keine Schreibaktionen.

## Entwurf 1: Technische Projektgrundstruktur

**Titel:** Technische Projektgrundstruktur nach Stackentscheidung einrichten

**Beschreibung**

Die technische Projektgrundlage soll nach ausdrücklicher Stackentscheidung eingerichtet werden. Zur Auswahl stehen Tauri mit React/TypeScript/Rust oder Electron mit React/TypeScript. Der Main Manager vergleicht die Optionen; dieser Entwurf legt keine Architektur fest. Die Stackzustimmung ist Voraussetzung für den Start.

**Priorität:** P1

**Abhängigkeiten:** Ausdrückliche Stackentscheidung; keine Abhängigkeit von Issue 2 oder 3.

**Akzeptanzkriterien**

- [ ] Der bestätigte Stack und die zugehörigen Laufzeit- und Buildvoraussetzungen sind dokumentiert.
- [ ] Das Projekt lässt sich mit den im Repository dokumentierten Befehlen lokal starten und bauen.
- [ ] Die minimale Anwendungsoberfläche startet ohne Laufzeitfehler.
- [ ] Reale Test-, Lint-, Typecheck- und Build-Einstiege sind vorhanden und dokumentiert; jeder Befehl wurde lokal ausgeführt.
- [ ] Die vorhandene CI-Vorlage wird erst mit diesen realen Befehlen aktiviert; ein Pull-Request-Lauf führt sie aus und schlägt bei einem fehlschlagenden Check fehl.

**Tests:** Nur synthetische oder temporäre Testdaten. Nach Stackfreigabe eine minimale echte Testinfrastruktur mit einem sinnvollen Start-/Grenzverhaltenstest einrichten; keine leere Suite als Erfolg werten. Der Main Manager schreibt passende fehlschlagende Tests vor Implementierung und verifiziert die finalen Befehle.

**Sicherheit:** Keine echten Nutzerdokumente, Schlüssel oder Zugangsdaten in Tests oder Projektdateien aufnehmen. Keine Dateiindizierung oder KI-Uploads ergänzen.

**Branch:** `feat/<issue-number>-project-foundation`

**PR:** `<PR-Link, später ergänzen>`

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
