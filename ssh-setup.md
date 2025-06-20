# SSH-Zugang auf einem Ubuntu Server (barebones) einrichten

Diese Anleitung beschreibt die Einrichtung eines sicheren SSH-Zugangs mit Schlüssel-Authentifizierung auf einem frisch installierten Ubuntu Server ohne vorinstallierte Werkzeuge.

## Voraussetzungen

- Ubuntu Server (barebones) ist installiert
- Zugriff auf den Server über Tastatur und Bildschirm
- Ein Benutzerkonto mit `sudo`-Rechten existiert
- Der Server ist mit dem lokalen Netzwerk verbunden
- Ein SSH-Client steht auf einem anderen Rechner bereit (Linux)

## 1. SSH-Server installieren

Auf dem Server:

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl enable --now ssh
````

Status des Dienstes prüfen:

```bash
sudo systemctl status ssh
```

## 2. IP-Adresse des Servers herausfinden

```bash
ip a
```

Oder:

```bash
hostname -I
```

Notiere die IP-Adresse des Servers, z. B. `192.168.178.45`. Diese wird im weiteren Verlauf benötigt.

## 3. SSH-Schlüsselpaar auf dem Client erzeugen

Auf dem Rechner, von dem aus auf den Server zugegriffen werden soll:

```bash
ssh-keygen -t ed25519 -C "heimserver"
```

* Der Standardpfad `~/.ssh/id_ed25519` kann übernommen werden.
* Eine Passphrase ist optional, wird aber empfohlen.

## 4. Öffentlichen Schlüssel auf den Server übertragen

### Variante A – Mit `ssh-copy-id` (wenn auf dem Client verfügbar)

```bash
ssh-copy-id <benutzername>@<server-ip>
```

**Hinweis:**

* `<benutzername>` = Benutzername des Servers (z. B. `admin`, `ubuntu`, o. Ä.)
* `<server-ip>` = IP-Adresse aus Schritt 2 (z. B. `192.168.178.45`)

→ Diese Werte müssen ersetzt werden. Kein direktes Copy-Paste möglich.

### Variante B – Manuell

#### 1. Auf dem Client:

```bash
cat ~/.ssh/id_ed25519.pub
```

Den gesamten Key kopieren.

#### 2. Auf dem Server (lokal):

Falls `nano` nicht installiert ist:

```bash
sudo apt install nano
```

Dann:

```bash
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

Den kopierten öffentlichen Schlüssel in die Datei einfügen, speichern und schließen.

Berechtigungen setzen:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

## 5. SSH-Verbindung testen

Auf dem Client:

```bash
ssh <benutzername>@<server-ip>
```

Wenn alles korrekt eingerichtet ist, erfolgt die Anmeldung ohne Passwort.

## 6. Passwort-Authentifizierung deaktivieren 

Erst deaktivieren, **wenn der SSH-Zugang mit Schlüssel erfolgreich funktioniert**.

```bash
sudo nano /etc/ssh/sshd_config
```

Folgende Einstellungen anpassen oder ergänzen:

```text
PasswordAuthentication no
PermitRootLogin no
```

Dann den SSH-Dienst neu starten:

```bash
sudo systemctl restart ssh
```

## 7. UFW-Firewall einrichten

```bash
sudo apt install ufw
sudo ufw allow OpenSSH
sudo ufw enable
```

Firewall-Status prüfen:

```bash
sudo ufw status
```

## Hinweis

* Platzhalter wie `<benutzername>` und `<server-ip>` müssen vor dem Ausführen ersetzt werden.
* SSH-Zugang sollte zuerst mit aktivem Passwort-Login getestet werden.
* Bei Verbindungsproblemen: lokale Verbindung prüfen, IP-Adresse kontrollieren, Logdateien einsehen (`journalctl -u ssh`).
* Authenticaton-Key irgendwo absichern (z.b. USB-Stick)
