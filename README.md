# Home Server Setup – Dokumentation

Dieses Repository dient der Dokumentation eines privaten Homeservers auf Basis eines Intel NUC mit Ubuntu Server (barebones). Ziel ist es, eine stabile, nachvollziehbar aufgebaute Umgebung für selbst gehostete Anwendungen zu schaffen. Alle Konfigurationen und Schritte werden hier versioniert dokumentiert.

## Ziel

- Einrichtung und Betrieb eines eigenen Homeservers
- Vollständige technische Dokumentation für Wartung und Wiederaufbau
- Sicherheit, Übersicht und Flexibilität als Grundlage für spätere Dienste

## Systemübersicht

- Hardware: Intel NUC
- Betriebssystem: Ubuntu Server 22.04 (barebones)
- Netzwerk:
  - Server über LAN an TP-Link Switch
  - Switch angebunden per Powerline an Router im Keller
  - Zugriff über das lokale Netzwerk (Headless-Betrieb)

## Dokumentierte Komponenten

### SSH-Zugang

Die Einrichtung eines sicheren SSH-Zugangs mit Schlüssel-Authentifizierung ist im Detail in [`ssh-setup.md`](./ssh-setup.md) dokumentiert.

Enthaltene Schritte:
- Installation des OpenSSH-Servers
- Schlüsselerzeugung auf dem Client
- Autorisierung auf dem Server
- Deaktivierung von Passwort-Login
- Einrichtung einer Firewall mit UFW

## Struktur des Repositories

```plaintext
.
├── README.md           # Projektübersicht und Zielbeschreibung
├── ssh-setup.md        # Detaillierte Anleitung zur SSH-Einrichtung
└── weitere Dateien     # folgen mit zunehmendem Ausbau
