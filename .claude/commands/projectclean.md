# /projectclean

Starte den ProjectClean-Skill für dieses Projekt.

## Verhalten

Nutze `SKILL.md` als Arbeitsgrundlage und führe den Ablauf in dieser Reihenfolge durch:

1. Projektkontext laden (Regeln, Stack, Versionsdateien, Testbefehle)
2. Versionsnummer erhöhen (alle zuständigen Dateien synchron)
3. Tests ausführen – bei Fehlern stoppen und fragen
4. Committen (nur relevante Dateien, kein `--no-verify`)
5. Pushen (kein Force-Push ohne Freigabe)
6. Dokumentation aktualisieren (CHANGELOG, Versionen, Deployment – detailliert)
7. Lokales Backup erstellen (mit Hashmanifest)
8. Ältere Backups löschen (mindestens 3 behalten)
9. Ordner aufräumen und Speicherplatz sparen

Wenn nicht alle Schritte gewünscht sind, frage kurz welche ausgeführt werden sollen.

## Sicherheitsregeln

- Keine Secrets, `.env`-Dateien oder personenbezogenen Daten in Git, Backups oder Doku.
- Dateien nur löschen, wenn sie eindeutig als temporär oder reproduzierbar identifiziert sind.
- Mindestens die letzten 3 vollständigen Backups erhalten.
- Kein Force-Push und keine Live-Aktionen ohne ausdrückliche Freigabe.

## Ergebnis

Am Ende kurz berichten:

- Neuer Versionsstand
- Test-Ergebnis
- Commit-Hash und Push-Status
- Aktualisierte Dokumentation
- Backup-Pfad und Retention
- Freigegebener Speicherplatz
- Offene Punkte

## 🔒 Lokal-only — Playtests, Backups & sensible Daten

Diese Daten dürfen **niemals** die lokale Maschine verlassen — weder nach GitHub noch nach Live/Deploy:

- **Playtests:** Play-Test-Branches (`PlayTest*`) und -Artefakte (`PlayTest/`, Protokolle, Screenshots) bleiben lokal.
- **Backups:** DB-Dumps, `*.sql`, `*.sql.gz`, `BACKUPS/` bleiben lokal — nie nach GitHub, nie in den Webroot/Live.
- **Sensible Daten:** `.env*` (außer `.env.example`), Tokens, API-Keys, Passwörter, `*.pem`, `*.key`, Zugangsdaten — niemals committen/pushen/deployen.
- **Push-Disziplin:** Nur den Hauptbranch (`main`) pushen, **niemals** `git push --all`/`--mirror`. `PlayTest*`-Branches werden nie gepusht.

Alle genannten Muster gehören in `.gitignore`. Technische Absicherung: der Pre-Push-Hook aus dem [DEV-Skill](https://github.com/MichaelGahnDESIGN/MGD_DEV_SKILL) (`dev/hooks/pre-push`) blockiert solche Pushes hart — empfohlen, am besten global via `git config --global core.hooksPath ~/.git-hooks`.
