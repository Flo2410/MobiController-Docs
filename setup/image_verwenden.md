---
layout: default
title: Verwenden des Prebuild Images
parent: Setup
---

# Verwenden des Prebuild Images

## Installation

1. Image herunterladen.
2. Flashen des Images auf eine SD-Karte, z. B. mit [balenaEtcher](https://www.balena.io/etcher)
3. SD-Karte in Raspberry stecken und Raspberry einschalten.
4. Hostname setzen:

   Zuerst muss der Hostname mittels der `hostnamectl` geändert werden.

   ```bash
   sudo hostnamectl hostname NEW_HOSTNAME
   ```

   Anschließend muss noch in der `/etc/hosts` Datei der Hostname angepasst werden.
   Dazu im Eintrag

   ```bash
   127.0.1.1       mobi-setup
   ```

   `mobi-setup` mit dem neuen Hostname austauschen.

   ```bash
   127.0.1.1       NEW_HOSTNAME
   ```

5. DHCP deaktivieren und entsprechende IP-Adresse setzten.

   Hier ist das `xx` mit dem entsprechenden Teil der IP-Adresse zu ersetzten.

   ```bash
   sudo nmcli connection modify Robotik\ Labor ipv4.addresses 10.94.160.xx/24
   ```

    ```bash
   sudo nmcli connection modify Robotik\ Labor ipv4.gateway 10.94.160.1
   ```

   ```bash
   sudo nmcli connection modify Robotik\ Labor ipv4.dns 10.94.32.11,10.30.0.11,10.30.0.12
   ```

   ```bash
   sudo nmcli connection modify Robotik\ Labor ipv4.method manual
   ```

6. Reboot

## Remote Access via VNC

1. VNC Client [hier](https://www.realvnc.com/en/connect/download/viewer/) herunterladen

## SSH X-Forwarding

1. Benötigte Software installieren:

   - Linux: Keine
   - Mac: [XQuartz](https://www.xquartz.org)
   - Windows: [Xming](https://www.straightrunning.com/XmingNotes/)

2. Mit SSH verbinden:

   - `-X`: X-Forwarding
   - `-C`: Kompression

   > **_ACHTUNG:_** Lokal ausführen

   ```bash
   ssh -XC mobi@10.94.160.xxx
   ```

## SSH mit Public Key Authentifikation (optional, empfohlen)

> **_ACHTUNG:_** Alle folgenden Befehle lokal ausführen und xx immer mit dem passen Teil der IP des Mobis ersetzen.

1. Key Pair generieren

   ```bash
   ssh-keygen -f ~/.ssh/mobi_ed25519 -t ed25519
   ```

2. SSH config bearbeiten

   Die lokale Datei ``~/.ssh/config`` öffnen und folgendes hinzufügen

   ```bash
   Host mobi
     HostName 10.94.160.xx
     User mobi
     IdentityFile= ~/.ssh/mobi_ed25519
     PreferredAuthentications publickey
     Compression yes
     ForwardX11 yes
   ```

3. SSH Key auf den Mobi laden
  
      MacOS & Linux:

      ```bash
      ssh-copy-id -i ~/.ssh/mobi_ed25519.pub mobi@10.94.160.xx
      ```

      Windows:

      ```bash
      type $env:USERPROFILE\.ssh\mobi_ed25519.pub | 
      ssh mobi@10.94.160.xx "cat >> .ssh/authorized_keys"
      ```
  
4. Die Verbindung testen

   Nun mit

   ```bash
   ssh mobi
   ```

   auf dem Mobi verbinden. Es sollte dabei kein Passwort benötigt werden.

## Remote Development mit VS Code

1. VS Code Extension [Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) installieren