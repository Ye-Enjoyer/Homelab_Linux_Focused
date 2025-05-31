# Homelab 

Diese Doku ist für das Homelab zuhause, barebones Ubuntu-Server. Stand 31.05.2025. Verbunden wird aus einem anderen Linux system.


## openssh auf server Installieren

~$ sudo apt update

~$ sudo apt install openssh-server


## Prüfen, ob SSH-Dienst läuft

~$ sudo systemctl status ssh

*#falls ssh nicht läuft*

~$ sudo systemctl enable ssh

~$ sudo systemctl start ssh


## Firewall für ssh konfigurieren

~$ sudo ufw allow ssh

#Nur wenn ufw noch nicht aktiviert ist

~$ sudo ufw enable   

~$ sudo ufw status


## Mit Server verbinden

~$ ssh serverusername@IP/Hostname

## SSH Zugang härten 

auf dem Server Root-Login deaktivieren

~$ sudo nano /etc/ssh/sshd_config

*PermitRootLogin no*
*PasswordAuthentication no*

## SSH_Schlüsselpaar generieren

auf eigenem Client

~$ ssh-keygen

Generating public/private rsa key pair.

Enter file in which to save the key (/home/dein-benutzer/.ssh/id_rsa): [ENTER drücken]

Enter passphrase (empty for no passphrase): [optional]

Enter same passphrase again: [optional]

ssh schlüsser zum server kopieren

~$ ssh-copy-id zeus@192.168.2.121

oder falls name gegeben wurde

~$ ssh-copy-id -i ~/Dateiname.pub serverusername@ip/hostname

wieder mit ssh anmelden

sollte nach keine Passwort fragen oder Passphrase falls erstellt

auf dem server kann man mit "cat ~/.ssh/authorized_keys" schauen, ob der key da ist

## falls für key-gen ein name vergeben wure QOL maßnahmen

auf dem eigenen Client

~$ nano ~/.ssh/config

Folgendes eingeben:
 
    Host serverusername-server 
     HostName "IPadresse"
     User "serverusername"
     IdentityFile "Pfad vom Key"

jetz kann man mit "~$ ssh serverusername-server" sich anmelden


## passphrase überspringen

Agent Starten

~$ eval "$(ssh-agent -s)"

Schlüssel zum Agent hinzufügen

~$ ssh-add ~/Dateipfad

passphrase eingeben damit der agent sich ihn merkt

dann mit "~$ ssh serverusername-server" anmelden, sollte ohne eingabe klappen
