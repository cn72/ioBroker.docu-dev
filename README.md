# ioBroker.docu-dev

## Inhaltsverzeichnis
1. Entwicklungsumgebeung

## Entwicklungsumgebeung

Für die Entwicklung von Adaptern ist es mehr als sinvoll, eine eigene Entwicklungsumgebung zu nutzen, um sich ein "Live-System" nicht zu beschädigen,
bzw. sein Entwicklungssystem gleich so einrichten zu können, um eine Verbindung zu GitHub und den ioBroker Reposity herzustellen, damit Updates etc.
ohne großen Aufwand veröffentlicht werden können. Solch ein Entwicklungsumgebung kann auf verschiedenen Systemen installiert werden. Es gibt halt 1000 Wege,
die nach Rom führen. Ob ein eigener Rechner verwendet wird, oder ob ich ein Linuxsystem auf einem virtuellen System aufsetze oder ob ich den DEV-Server auf einem Windowsrechner verwende ist prinzipiell egal. Da viele ja ioBroker bereits am laufn haben und sich irgendwie da schon Gedanken zu gemacht haben ist oftmals der ein oder andere mit einem Raspberry angefangen und hat dadurch den ein oder anderen Raspberry noch in der Schublade liegen. Ich habe mich hier für einen Raspberry Pi 4 entschieden - als Rechner für den DEV-Server und als Entwicklungs-Editor den "VisualCode Studio" Editor
auf einem Windows Rechner. Installationssystem ist ein RaspberryPi 4 mit einer 16GB SD-Karte. Mit Hilfe des Raspberry Pi Imager wird das OS ohne Desktop installiert.
Beim Imager wird als OS "Raspberry Pi OS (other)" und dann "Raspberry Pi OS Lite (64bit)" ausgewählt. Nach Auswahl der SD-Karte hat man die Möglichkeit,
die Einstellungen anzupassen. Folgende Einstellungen habe ich hier vorgenommen:
Tab Allgemein: Hostname-> iodev.local; Benutzername -> pi; Passwort-> raspberry; WIFI-> das am Standort verwendete und unter Passwort das WLAN Passwort eingeben;WIFI Land-> DE;Zeitzone->Europe/Berlin; Tastaturlayout-> de
Tab Dienste: SSH -> aktivieren; Passwort zur Authentifizierung verwenden->auwählen
Danach speichern und danach 2x mit ja bestätigen.
Nach ein paar Minuten ist das Image auf die SD-Karte geschrieben und kann in den Pi4 eingesetzt werden. Jetzt noch Strom und der Pi4 startetund sollte sich mitden lokalen WLan verbinden. Am Router kann die IP-Adresse ermittelt werden.
Um aufden Raspberry zugreifen zu können wird ein Terminal-Programm verwendet. Hier verwende ich Putty.

Mit:
```
sudo apt-get update
sudo apt-get upgrade
```
bringen wir das System erst einmal auf den aktuellen Stand.
Al nächstes werden noch einige Pakete installiert.
```
sudo apt install -y git vim net-tools curl
```
danach installieren wir node !!! ACHTUNG !!! bitte schauen, welche Version aktuell ist und die 20 durch die aktuelle Version ersetzen
```
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
```
und danach
```
sudo apt install -y gcc g++ make
```
sowie
```
sudo apt install -y nodejs
```
Bevor der Dev-Server installiert werden kann sind folge Befehle auszuführen:
```
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
nano ~/.profile
```
und im Editor mit der Cursor-Taste ganz anz Ende scrolen und folgende Zeile anhängen
```
export PATH=~/.npm-global/bin:$PATH
```
mit Strg + x -> y -> Enter den Editor verlassen. Nun die Profile-Einstellungen neu laden (oder ab und anmelden bzw. das System komplett neu starten)
```
source ~/.profile
```
und zu guter letzt wird nun der ioBroker-dev Server installiert
```
npm install --global @iobroker/dev-server
```
Wer jetzt seine SD-Karte nimmt und mit Etcher eine Sicherung erstellt, kann immer wieder den Server neu aufsetzen, ohne die Installationen durchführen zu müssen.
Nun wollen wir unser Entwicklungssystem noch mit unserem GitHub account verbinden. Wer noch keinen GitHub Account hat, sollte dies nun nachholen.
Wir benötigen einen Key, damit der Rechner die Daten zu GitHub senden kann. Diesen erstellen wir mit
```
ssh-keygen -t ed25519 -C "Lenovo T570"
```
mit
