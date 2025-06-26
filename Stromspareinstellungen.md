# Stromsparmaßnahmen im Idle-Betrieb (Powertop Auto-Tuning)

Diese Konfiguration aktiviert beim Systemstart automatische Stromsparoptimierungen mit `powertop`. Sie ist für den dauerhaften Serverbetrieb geeignet, da sie keine Dienste (wie Nextcloud, SSH oder Docker) beeinträchtigt.

## Ziel

Reduzierung des Stromverbrauchs im Leerlauf (Idle), ohne die Erreichbarkeit oder Funktionalität des Servers zu gefährden.

## Voraussetzungen

- Ubuntu Server 22.04 oder neuer
- root- oder sudo-Rechte

## Installation von Powertop

```bash
sudo apt update
sudo apt install powertop
````

## Systemd-Service für automatische Optimierung

### 1. Service-Datei erstellen

```bash
sudo nano /etc/systemd/system/powertop-tune.service
```

### 2. Inhalt einfügen

```ini
[Unit]
Description=Apply PowerTOP tunings
After=multi-user.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/powertop --auto-tune

[Install]
WantedBy=multi-user.target
```

Der Pfad zu `powertop` kann mit `which powertop` überprüft werden.

### 3. Service aktivieren und starten

```bash
sudo systemctl daemon-reexec
sudo systemctl enable powertop-tune.service
sudo systemctl start powertop-tune.service
```

Der Service wird nun bei jedem Boot automatisch ausgeführt.

## Status prüfen

```bash
systemctl status powertop-tune.service
```

Ein `inactive (dead)`-Status nach der Ausführung ist normal, da der Dienst nur einmal beim Boot läuft.

Powertop setzt jetzt automatisch empfohlene Energiesparoptionen für unterstützte Komponenten (z. B. USB, PCIe, SATA). Die Änderungen betreffen nur Einstellungen, die den Betrieb nicht einschränken.
