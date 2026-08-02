---
name: projectclean
description: Use when the user invokes /projectclean or asks to bump the version, run tests, commit, push, update documentation, create a local backup, delete old backups, or clean up a project folder to save disk space. Can be used for any stage of this workflow or for the full sequence end to end.
---

# ProjectClean

Führt ein Projekt durch den vollständigen Abschluss- und Aufräum-Workflow: Versionsnummer erhöhen, testen, committen, pushen, Dokumentation aktualisieren, lokales Backup erstellen, alte Backups löschen, Ordner aufräumen und Speicherplatz sparen.

Der Skill ist projektneutral. Stack, Testbefehle, Backup-Pfade, Dokumentationsstruktur und Versionsdateien werden immer aus dem jeweiligen Projekt abgeleitet.

## Trigger

- `/projectclean` – führt den vollständigen Ablauf aus.
- Normaler Text zählt ebenfalls: „Räum das Projekt auf", „Erhöhe die Version und deploy", „Backup erstellen und alte löschen", „Speicherplatz sparen".
- Einzelne Schritte können gezielt aufgerufen werden: „Nur testen und committen", „Nur Backup und Cleanup".

## Harte Sicherheitsregeln

- Keine Secrets, Tokens, Passwörter, `.env`-Dateien oder personenbezogenen Daten in Git, Backups, Logs oder Dokumentation schreiben.
- Keine Dateien löschen, die nicht eindeutig als temporär oder reproduzierbar identifiziert sind.
- Backups erst löschen, nachdem ein neues vollständiges Backup erfolgreich angelegt und geprüft wurde.
- Mindestens die letzten 3 vollständigen Backups je Backup-Familie erhalten.
- Keine Live-Deployments, Datenbankoperationen oder externen Nebenwirkungen ohne ausdrückliche Freigabe.
- Bei Widersprüchen zwischen lokalem Stand und Remote zuerst dokumentieren, nicht überschreiben.

## Startabfrage

Frage nur, was nicht aus dem Kontext hervorgeht:

1. Welcher Teil des Workflows soll ausgeführt werden – alles oder einzelne Schritte?
2. Welche Versionsdatei gilt: `package.json`, `VERSION`, `pubspec.yaml`, `pyproject.toml`, `Cargo.toml`, andere?
3. Welche Testbefehle soll der Agent nutzen?
4. Wo liegt der Dokumentationsordner: `DOKUMENTATION/`, `docs/`, Wiki, andere?
5. Wo sollen Backups abgelegt werden und wie heißt die Backup-Familie?
6. Welche Ordner oder Dateitypen dürfen beim Cleanup gelöscht werden?

Wenn der Nutzer bereits genug geliefert hat, starte direkt.

## Ablauf

### 1. Projektkontext laden

Lies vor Änderungen:

- `AGENTS.md`, `CLAUDE.md`, `README.md`, Projektregeln und vorhandene Dokumentation;
- Paket- und Stack-Hinweise wie `package.json`, `pubspec.yaml`, `pyproject.toml`, `Cargo.toml`, `go.mod`;
- Vorhandene Backup-, Deploy- und Testdokumentation;
- `git status --short --branch` und aktuelle Tags.

### 2. Versionsnummer erhöhen

Ermittle die aktuelle Version aus der zuständigen Datei. Erhöhe die Versionsnummer nach dem Projekt-Schema (Semantic Versioning empfohlen: `MAJOR.MINOR.PATCH`).

Typische Kandidaten:

- `package.json` → `"version": "x.y.z"`
- `VERSION` → einzeilige Versionsdatei
- `pubspec.yaml` → `version: x.y.z`
- `pyproject.toml` → `version = "x.y.z"`
- `Cargo.toml` → `version = "x.y.z"`

Wenn mehrere Versionsdateien existieren, prüfe ob sie synchron sind und aktualisiere alle gemeinsam. Wenn unklar ist, ob MAJOR, MINOR oder PATCH erhöht werden soll, frage kurz nach.

### 3. Tests ausführen

Führe die projektspezifischen Tests aus. Typische Befehle je Stack:

- Node.js / TypeScript: `npm test`, `npm run check`, `npm run lint`
- Python: `pytest`, `python -m pytest`, `ruff check`
- Flutter: `flutter test`, `flutter analyze`
- Rust: `cargo test`
- PHP: `php -l`, `./vendor/bin/phpunit`
- Allgemein: vorhandene CI-Skripte oder dokumentierte Testbefehle

Wenn Tests fehlschlagen: Befund dokumentieren und fragen, ob fortgefahren oder abgebrochen werden soll. Nie über Fehler hinweggehen.

### 4. Committen

Erstelle einen aussagekräftigen Commit:

- Staginge nur relevante Dateien – keine `.env`, `node_modules`, Build-Artefakte oder Secrets.
- Commit-Nachricht nennt den Anlass: Version, Feature, Fix, Cleanup.
- Wenn Hooks fehlschlagen: Ursache klären, nicht mit `--no-verify` umgehen.

Beispiel-Commit-Nachricht:
```
chore: release v1.2.0 – cleanup und Dokumentation aktualisiert
```

### 5. Pushen

Push auf den vorgesehenen Branch:

- Bevorzuge normalen Push ohne Force.
- Prüfe vorher `git status` und `git log --oneline -3`.
- Wenn der Remote abweicht: erst dokumentieren, dann klären.
- Kein Force-Push auf geteilten Branches ohne ausdrückliche Freigabe.

### 6. Dokumentation aktualisieren

Aktualisiere den projektinternen Dokumentationsordner. Typische Ziele:

- `DOKUMENTATION/Projektbetrieb/Versionen.md` – neue Version, Datum, Änderungen
- `DOKUMENTATION/Projektbetrieb/Backups.md` – Backup-Protokoll
- `DOKUMENTATION/Projektbetrieb/Deployment.md` – letzter Deploy-Stand
- `CHANGELOG.md` – neuer Eintrag mit Datum und Änderungsliste
- `README.md` – Versions-Badge oder Changelog-Abschnitt, falls vorhanden

Schreibe detailliert:

- Was wurde geändert?
- Welche Version ist jetzt aktiv?
- Welche Tests wurden ausgeführt und was war das Ergebnis?
- Was ist offen geblieben?

Keine Secrets, internen Serverpfade, Zugangsdaten oder personenbezogenen Daten in die Dokumentation schreiben.

### 7. Lokales Backup erstellen

Erstelle ein vollständiges lokales Backup:

- Backup-Pfad aus Projektdokumentation ableiten oder beim Nutzer erfragen.
- Backup-Name mit Datum und Versionsnummer: `Projektname_v1.2.0_YYYY-MM-DD.zip` oder vergleichbares Schema.
- Datenbankdump einschließen, wenn eine Datenbank betroffen ist.
- Uploads, Medien oder andere nutzergenerierte Inhalte einschließen, wenn sie nicht reproduzierbar sind.
- Hashmanifest (SHA-256 oder gleichwertig) erstellen und prüfen.
- Backup-Pfad und Hash im Abschlussbericht nennen.

### 8. Ältere Backups löschen

Inventarisiere alle vorhandenen Backups der betroffenen Familie:

- Liste alle Backup-Dateien nach Datum sortiert auf.
- Behalte mindestens die letzten 3 vollständigen, geprüften Backups.
- Lösche nur ältere Backups, die eindeutig von neueren vollständig abgedeckt werden.
- Nie löschen: Backups, die als Restore-Punkte in der Dokumentation referenziert sind; unvollständige oder ungeprüfte Backups; Backups mit rechtlich relevanten Inhalten ohne Freigabe.
- Dokumentiere, welche Backups gelöscht wurden und warum.

### 9. Ordner aufräumen und Speicherplatz sparen

Erstelle eine Inventarliste, bevor gelöscht wird:

**Typische Kandidaten für sicheres Löschen:**

- `node_modules/`, `.venv/`, `.dart_tool/`, `vendor/` – reproduzierbar über Paketmanager
- Build-Artefakte: `dist/`, `build/`, `.build/`, `target/` – reproduzierbar über Build-Befehle
- Cache-Verzeichnisse: `.cache/`, `__pycache__/`, `.parcel-cache/`
- Temporäre Dateien: `*.log`, `*.tmp`, `.DS_Store`, `Thumbs.db`
- Alte Worktrees und zusammengeführte lokale Branches

**Niemals löschen:**

- Nutzeruploads, Medien oder Rechnungsdaten ohne klare Freigabe
- Unbekannte Dateien, die Secrets oder persönliche Daten enthalten könnten
- Dateien, die in der Dokumentation als Restore-Punkt referenziert sind
- Dateien, deren Zweck unklar ist – lieber kurz fragen

Berichte, wie viel Speicherplatz freigegeben wurde.

## Abschlussbericht

Berichte knapp:

- Projektname und neuer Versionsstand
- Ausgeführte Tests und Ergebnis
- Commit-Hash und Push-Status
- Aktualisierte Dokumentationsdateien
- Backup-Pfad, Umfang, Hash und Retention-Entscheidung
- Freigegebener Speicherplatz
- Bekannte Restrisiken oder bewusst offene Punkte

## 🔒 Lokal-only — Playtests, Backups & sensible Daten

Diese Daten dürfen **niemals** die lokale Maschine verlassen — weder nach GitHub noch nach Live/Deploy:

- **Playtests:** Play-Test-Branches (`PlayTest*`) und -Artefakte (`PlayTest/`, Protokolle, Screenshots) bleiben lokal.
- **Backups:** DB-Dumps, `*.sql`, `*.sql.gz`, `BACKUPS/` bleiben lokal — nie nach GitHub, nie in den Webroot/Live.
- **Sensible Daten:** `.env*` (außer `.env.example`), Tokens, API-Keys, Passwörter, `*.pem`, `*.key`, Zugangsdaten — niemals committen/pushen/deployen.
- **Push-Disziplin:** Nur den Hauptbranch (`main`) pushen, **niemals** `git push --all`/`--mirror`. `PlayTest*`-Branches werden nie gepusht.

Alle genannten Muster gehören in `.gitignore`. Technische Absicherung: der Pre-Push-Hook aus dem [DEV-Skill](https://github.com/MichaelGahnDESIGN/MGD_DEV_SKILL) (`dev/hooks/pre-push`) blockiert solche Pushes hart — empfohlen, am besten global via `git config --global core.hooksPath ~/.git-hooks`.
