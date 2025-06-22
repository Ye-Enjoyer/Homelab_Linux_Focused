# Schutz mit Fail2Ban auf Ubuntu Server

Diese Anleitung beschreibt die Einrichtung von [Fail2Ban](https://www.fail2ban.org/), um den SSH-Zugang und andere Dienste des Servers automatisch vor Brute-Force-Angriffen zu schützen.

---

## Was macht Fail2Ban?

Fail2Ban überwacht Logdateien wie `/var/log/auth.log` und blockiert IP-Adressen automatisch, wenn zu viele fehlgeschlagene Anmeldeversuche erkannt werden.

Beispiel:
→ 5 falsche SSH-Logins in 5 Minuten → IP wird für 30 Minuten gesperrt.

---

## Installation

```bash
sudo apt update
sudo apt install fail2ban
```

---

## Konfiguration

Die Datei `jail.local` enthält die benutzerdefinierte Konfiguration.

```bash
sudo nano /etc/fail2ban/jail.local
```

Inhalt:

```ini
[sshd]
enabled = true
port = ssh
logpath = %(sshd_log)s
backend = %(sshd_backend)s
bantime = 30m
findtime = 5m
maxretry = 5
```

Erklärung:

| Einstellung | Beschreibung                             |
| ----------- | ---------------------------------------- |
| `enabled`   | Aktiviert die Jail für SSH               |
| `port`      | Überwachter Port (Standard: 22)          |
| `logpath`   | Logdatei für SSH-Fehlversuche            |
| `bantime`   | Dauer der Sperre (z. B. 30 Minuten)      |
| `findtime`  | Zeitraum für max. Versuche (z. B. 5 Min) |
| `maxretry`  | Anzahl zulässiger Fehlversuche           |

---
#Weitere Regeln können einfach untereinander aufgelistet werden (z.b. Nexcloud)

## Fail2Ban starten

```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

Status prüfen:

```bash
sudo systemctl status fail2ban
sudo fail2ban-client status sshd
```

Beispielausgabe:

```
Status for the jail: sshd
|- Filter
|  └─ Currently failed: 0
|- Actions
|  └─ Currently banned: 1
```

---

## Ergebnis

SSH-Brute-Force-Angriffe werden automatisch blockiert.
Bei legitimer Nutzung mit SSH-Keys ist keine Beeinträchtigung zu erwarten.
