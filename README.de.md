# Lisa's Notes

Ein Claude-Code-Plugin, das jede abgeschlossene Claude-Interaktion automatisch als Commit in der [Jujutsu](https://github.com/martinvonz/jj)-Versionskontrolle aufzeichnet.

## Warum „Lisa's Notes"?

Genau wie Lisa Simpson alles akribisch in ihr Tagebuch einträgt, führt dieses Plugin eine detaillierte Aufzeichnung jeder Änderung, die Claude an Ihrer Codebasis vornimmt. Jedes Mal, wenn Sie die Arbeit mit Claude beenden, erstellt das Plugin automatisch einen Jujutsu-Commit und verwendet Claudes letzte Antwort als Commit-Nachricht – so geht nichts verloren oder in Vergessenheit.

## So funktioniert es

Das Plugin klinkt sich in Claudes „Stop"-Ereignis ein. Wenn Sie eine Claude-Sitzung beenden:

1. Das Plugin liest Claudes Gesprächsprotokoll (eine JSON-Lines-Datei)
2. Es analysiert jede Zeile und sucht nach Assistenten-Nachrichten
3. Es extrahiert den **vollständigen Textinhalt** der letzten Assistenten-Nachricht (alle Textblöcke werden zusammengeführt)
4. Es erstellt einen Jujutsu-Commit mit dieser vollständigen Nachricht als Commit-Nachricht
5. Alle Vorgänge werden zur Fehlerbehebung in `/tmp/lisas-notes-debug.log` protokolliert

Das bedeutet, dass jede Interaktion mit Claude zu einem eigenständigen, gut dokumentierten Commit in Ihrer Versionshistorie führt.

## Voraussetzungen

- [Jujutsu (jj)](https://github.com/martinvonz/jj) installiert und im PATH verfügbar
- [jq](https://stedolan.github.io/jq/) für das JSON-Parsing
- Ihr Projekt muss ein Jujutsu-Repository sein

## Installation

1. Dieses Repository klonen:
   ```bash
   git clone <repository-url> lisas-notes
   ```

2. Den Marketplace bei Claude mit dem Pfad zur `.claude-plugin/marketplace.json` registrieren

3. Das lisas-notes-Plugin im Marketplace aktivieren

## Verwendung

Nach der Aktivierung arbeitet das Plugin vollautomatisch:

1. Arbeiten Sie mit Claude in einem Jujutsu-verwalteten Repository
2. Wenn Sie die Claude-Sitzung beenden (Strg+C oder `/exit`), wird das Plugin ausgelöst
3. Ihre Änderungen werden mit Claudes letzter Antwort als Nachricht committed

Kein manuelles Eingreifen erforderlich – arbeiten Sie einfach mit Claude und lassen Sie Lisa die Aufzeichnungen führen.

## Projektstruktur

```
lisas-notes/
├── .claude-plugin/
│   └── marketplace.json      # Marketplace-Konfiguration
├── plugins/
│   └── lisas-notes/
│       ├── .claude-plugin/
│       │   └── plugin.json   # Plugin-Metadaten (v1.0.5)
│       ├── hooks/
│       │   └── hooks.json    # Stop-Hook-Definition
│       └── scripts/
│           └── auto-commit.sh # Das Commit-Automatisierungsskript
└── README.md
```

## Fehlerbehebung

Falls keine Commits erstellt werden, prüfen Sie das Debug-Protokoll:

```bash
tail -f /tmp/lisas-notes-debug.log
```

Dieses Protokoll zeigt:
- Wann der Hook startet und endet
- Den gelesenen Transkript-Pfad
- Die extrahierte Commit-Nachricht
- Eventuelle Fehler des `jj commit`-Befehls

## Autor

Ian Bull
