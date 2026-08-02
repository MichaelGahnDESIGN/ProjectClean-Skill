# ProjectClean-Skill

Ein allgemeiner Abschluss- und Aufräum-Skill für KI-Agenten.

Der Skill führt ein Projekt durch einen kontrollierten Abschluss-Workflow: Versionsnummer erhöhen, testen, committen, pushen, Dokumentation aktualisieren, lokales Backup erstellen, alte Backups löschen und den Projektordner aufräumen.

Er ist bewusst projektneutral gehalten und funktioniert mit beliebigen Stacks, Versionsdateien, Testbefehlen und Dokumentationsstrukturen.

## Für wen ist das gedacht?

Für Menschen, die mit KI-Agenten entwickeln und am Ende eines Arbeitsstands einen sauberen, dokumentierten und gesicherten Zustand herstellen wollen – ohne jeden Schritt manuell aufzählen zu müssen.

Der Skill ist besonders hilfreich, wenn:

- eine neue Version veröffentlicht werden soll,
- ein Projekt übergeben oder pausiert wird,
- Speicherplatz gespart werden soll,
- das lokale Backup-Archiv bereinigt werden soll,
- die Projektdokumentation nach einer Änderungsrunde aktualisiert werden soll.

## Schnellstart

```bash
# Claude Code
git clone https://github.com/MichaelGahnDESIGN/MGD_ProjectClean_SKILL.git ~/.claude/skills/projectclean

# ChatGPT Codex
git clone https://github.com/MichaelGahnDESIGN/MGD_ProjectClean_SKILL.git ~/.codex/skills/projectclean
```

Danach in einem beliebigen Projekt:

```text
/projectclean
```

Oder gezielt einzelne Schritte:

```text
/projectclean
Erhöhe nur die Version, teste und committe. Backup und Cleanup später.
```

## Ablauf

Der Skill führt den Agenten durch neun Bereiche:

1. **Projektkontext laden** – Regeln, Stack, Versionsdateien, Testbefehle, Git-Stand.
2. **Versionsnummer erhöhen** – alle zuständigen Dateien synchron aktualisieren.
3. **Tests ausführen** – projektspezifische Testbefehle; bei Fehlern stoppen und fragen.
4. **Committen** – nur relevante Dateien, aussagekräftige Commit-Nachricht.
5. **Pushen** – kein Force-Push ohne Freigabe, Konflikte nicht blind lösen.
6. **Dokumentation aktualisieren** – CHANGELOG, Versionen, Deployment, README-Badges; detailliert.
7. **Lokales Backup erstellen** – mit Hashmanifest, Datum und Versionsnummer im Dateinamen.
8. **Ältere Backups löschen** – mindestens 3 vollständige Backups je Familie behalten.
9. **Ordner aufräumen** – `node_modules`, Build-Artefakte, Caches, Logs; Speicherplatz-Bericht.

Die Reihenfolge ist wichtig: Backup vor Cleanup, Tests vor Commit.

## Versionsdateien

Der Skill erkennt und aktualisiert automatisch:

| Stack | Datei |
| --- | --- |
| Node.js / TypeScript | `package.json` |
| Python | `pyproject.toml`, `setup.py` |
| Flutter / Dart | `pubspec.yaml` |
| Rust | `Cargo.toml` |
| Allgemein | `VERSION` |

Wenn mehrere Dateien vorhanden sind, werden alle synchron aktualisiert.

## Sicherheitsprinzipien

- Erst sichern, dann löschen.
- Erst testen, dann committen.
- Keine Secrets, `.env`-Dateien oder personenbezogenen Daten in Git, Backups oder Dokumentation.
- Mindestens 3 vollständige Backups je Familie erhalten.
- Keine Force-Pushes und keine produktiven Nebenwirkungen ohne Freigabe.
- Unbekannte Dateien lieber einmal zu viel fragen als einmal zu schnell löschen.

## Slash-Command-Vorlagen

```text
.claude/commands/projectclean.md
.codex/commands/projectclean.md
```

Diese Dateien sorgen dafür, dass `/projectclean` in Claude Code und ChatGPT Codex als direkter Befehl erkannt wird.

## Repository-Struktur

```text
ProjectClean-Skill/
├── SKILL.md
├── README.md
├── LICENSE
├── .gitignore
├── .claude/
│   └── commands/
│       └── projectclean.md
└── .codex/
    └── commands/
        └── projectclean.md
```

## Updates

```bash
# Claude Code
cd ~/.claude/skills/projectclean && git pull

# ChatGPT Codex
cd ~/.codex/skills/projectclean && git pull
```

## Verwandte Projekte Von Michael Gahn DESIGN

ProjectClean gehört zu einer kleinen Werkzeugfamilie für KI-gestützte
Projektarbeit.

- [AI-Basic-Projektordner](https://github.com/MichaelGahnDESIGN/MGD_AI-Basic-Projektordner_TOOL)  
  Eine saubere Projektvorlage mit Regeln, Dokumentation, Agentenstruktur und
  Sicherheitsgrenzen. Sinnvoll als Basis für neue Projekte.

- [AI Project Updater Skill](https://github.com/MichaelGahnDESIGN/MGD_AI-Project-Updater_SKILL)  
  Ein geführter Assistent für lokale Staging-Umgebungen, Docker-Planung,
  Update-Vorbereitung und sichere Staging-zu-Live-Abläufe. Das Repository ist
  während der Entwicklung zunächst privat.

- [DEV-Skill](https://github.com/MichaelGahnDESIGN/MGD_DEV_SKILL)  
  Ein projektneutraler Skill für Projekt-Sync, Tests, GitHub-Abgleich,
  Deploy-Vorbereitung, Backups und Abschlussberichte.

- [AI-PlayTest-Skill](https://github.com/MichaelGahnDESIGN/MGD_AI-PlayTest_SKILL)  
  Ein Skill für Play-Tests aus Sicht echter Nutzerrollen, lokal, auf Staging
  oder vorsichtig auf Live.

- [Claude-Codex-MCP](https://github.com/MichaelGahnDESIGN/MGD_Claude-Codex_MCP)  
  Ein lokales MCP-System für Aufgaben, Chat und Übergaben zwischen Claude,
  Codex und weiteren KI-Agenten.

## Lizenz

MIT Lizenz. Siehe [LICENSE](LICENSE).

## 🔒 Lokal-only — Playtests, Backups & sensible Daten

Diese Daten dürfen **niemals** die lokale Maschine verlassen — weder nach GitHub noch nach Live/Deploy:

- **Playtests:** Play-Test-Branches (`PlayTest*`) und -Artefakte (`PlayTest/`, Protokolle, Screenshots) bleiben lokal.
- **Backups:** DB-Dumps, `*.sql`, `*.sql.gz`, `BACKUPS/` bleiben lokal — nie nach GitHub, nie in den Webroot/Live.
- **Sensible Daten:** `.env*` (außer `.env.example`), Tokens, API-Keys, Passwörter, `*.pem`, `*.key`, Zugangsdaten — niemals committen/pushen/deployen.
- **Push-Disziplin:** Nur den Hauptbranch (`main`) pushen, **niemals** `git push --all`/`--mirror`. `PlayTest*`-Branches werden nie gepusht.

Alle genannten Muster gehören in `.gitignore`. Technische Absicherung: der Pre-Push-Hook aus dem [MGD-DEV-Skill](https://github.com/MichaelGahnDESIGN/MGD_DEV_SKILL) (`dev/hooks/pre-push`) blockiert solche Pushes hart — empfohlen, am besten global via `git config --global core.hooksPath ~/.git-hooks`.

---

## Verwandte MGD Projekte

| Projekt | Beschreibung |
|---------|-------------|
| [MGD-DEV-Skill](https://github.com/MichaelGahnDESIGN/MGD_DEV_SKILL) | Release, Sync, Backup und Wissensdokumentation |
| [MGD-App-Updater-Skill](https://github.com/MichaelGahnDESIGN/MGD_Software-Updater_SKILL) | Software-Update-Systeme planen und implementieren |
| [MGD-Backup-Skill](https://github.com/MichaelGahnDESIGN/MGD_Backup_SKILL) | Automatisierte lokale und Cloud-Backups |
| [MGD-AI-Project-Updater-Skill](https://github.com/MichaelGahnDESIGN/MGD_AI-Project-Updater_SKILL) | Geführter Projekt-Assistent für Staging und Updates |
| [MGD-AI-Basic-Projektordner](https://github.com/MichaelGahnDESIGN/MGD_AI-Basic-Projektordner_TOOL) | Projektvorlage für KI-Agenten |

→ Alle öffentlichen Projekte: [github.com/MichaelGahnDESIGN](https://github.com/MichaelGahnDESIGN)

---

## Impressum

Angaben gemäß § 5 DDG — Siehe [`IMPRESSUM.md`](IMPRESSUM.md).
