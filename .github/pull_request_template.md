## Issue
Refs #<issue-number>. Feature-/Fix-PR nach `dev`; PR-Link auch im Issue ergänzen.
Keinen automatischen Issue-Abschluss durch Closing-Keywords bei Nicht-Default `dev` annehmen.
Abschluss erst nach bestätigtem Merge und erfüllten Akzeptanzkriterien.

## Änderung und Akzeptanzkriterien

## Verifikation
- Lokale Befehle und tatsächliche Ergebnisse:
- CI am finalen Head:
- Vom Main Manager geprüfter vollständiger Head-SHA:
- Geprüfter Base-SHA:

## Review
- [ ] Funktionalität, bestehende und neue Tests geprüft
- [ ] Architektur, unerwünschte Änderungen, unnötiger Code/Abhängigkeiten geprüft
- [ ] Datenschutz, Dateizugriff, destruktive Aktionen und API-Schlüssel geprüft
- [ ] Keine offenen Review-Blocker oder Merge-Konflikte

## Freigabe
Main-Manager-Freigabe und zusätzliche Nutzerbestätigung für exakt diesen Head.
Neue Änderungen machen eine frühere Freigabe ungültig. Automatische Freigabe ist deaktiviert.
