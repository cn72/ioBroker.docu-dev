# ioBroker.docu-dev

## Inhaltsverzeichnis
1. [Einleitung](#einleitung)
2. [Verweise](#verweise)
3. [Entwicklungsumgebung](#entwicklungsumgebung)

## Einleitung
Diese Docu richtet sich an alle Interessierte, die eine Erweiterung (Adapter) für den ioBroker programmieren / entwikeln wollen.
Viele befassen sich mit der SmartHome Technik als Hobby und der ein oder andere steigt tiefer ein. Hier soll ein Grundgerüst an die Hand gegeben werden,
mit dem es auch mit wenig Vorkenntnissen es möglich sein wird, einen Adapter zu erstellen. Diese Anleitung soll etwas Licht ins dunkle bringen,
kann aber natürlich nicht alle Möglichkeiten abbilden (bei weitem nicht), die es bei der Erstellung von Funktionen eines Adapters gibt.
Aber zur Erlangung von Grundkenntnissen ist diese Anleitung ein guter Einstieg.

[##Top](#inhaltsverzeichnis)

## Verweise
Hier will ich eine kleine  Auflistung geben, wo man noch an Infos herankommt, welche von interesse sein könnten, bzw. Lücken im Hintergrundwissen stopfen...;)

- [ioBroker.dev Entwicklungsseite mit Verlinkung auf Massenweise Info](https://www.iobroker.dev/) Plicht
- 
[##Top](#inhaltsverzeichnis)

## Entwicklungsumgebung

Für die Entwicklung von Adaptern ist es mehr als sinvoll, eine eigene Entwicklungsumgebung zu nutzen, um sich ein "Live-System" nicht zu beschädigen,
bzw. sein Entwicklungssystem gleich so einrichten zu können, um eine Verbindung zu GitHub und den ioBroker Reposity herzustellen, damit Updates etc.
ohne großen Aufwand veröffentlicht werden können. Solch ein Entwicklungsumgebung kann auf verschiedenen Systemen installiert werden. Es gibt halt 1000 Wege,
die nach Rom führen. Ob ein eigener Rechner verwendet wird, oder ob ich ein Linuxsystem auf einem virtuellen System aufsetze oder ob ich für den DEV-Server
einem Windowsrechner verwende ist prinzipiell egal. Da viele ja ioBroker bereits am laufn haben und sich irgendwie da schon Gedanken zu gemacht
haben ist oftmals der ein oder andere mit einem Raspberry angefangen und hat dadurch den ein oder anderen Raspberry noch in der Schublade liegen.
Ich habe mich hier für einen Raspberry Pi 4 entschieden - als Rechner für den DEV-Server und als Entwicklungs-Editor den "VisualCode Studio" Editor
auf einem Windows Rechner. Installationssystem ist ein RaspberryPi 4 mit einer 16GB SD-Karte. Mit Hilfe des Raspberry Pi Imager wird das OS ohne Desktop installiert.
Beim Imager wird als OS "Raspberry Pi OS (other)" und dann "Raspberry Pi OS Lite (64bit)" ausgewählt. Nach Auswahl der SD-Karte hat man die Möglichkeit,
die Einstellungen anzupassen. Folgende Einstellungen habe ich hier vorgenommen:
Tab Allgemein: Hostname-> iodev.local; Benutzername -> pi; Passwort-> raspberry; WIFI-> das am Standort verwendete und unter Passwort das WLAN Passwort eingeben;WIFI Land-> DE;Zeitzone->Europe/Berlin; Tastaturlayout-> de
Tab Dienste: SSH -> aktivieren; Passwort zur Authentifizierung verwenden->auwählen
Danach speichern und danach 2x mit ja bestätigen.
Nach ein paar Minuten ist das Image auf die SD-Karte geschrieben und kann in den Pi4 eingesetzt werden. Jetzt noch Strom und der Pi4 startetund sollte sich mit dem lokalen WLan verbinden. Am Router kann die IP-Adresse ermittelt werden.
Um auf den Raspberry zugreifen zu können wird ein Terminal-Programm verwendet. Hier verwende ich Putty.

Wir melden uns als Benutzer: pi an, nicht als root. Hat man zum Beispiel ein Linuxsystem auf einer virtuellen Umgebung installiert, bitte einen Benutzer anlegen und sudo installieren / Benutzer zuordnen und dann mit diesem Benutzer arbeiten.


Mit:
```
sudo apt-get update
sudo apt-get upgrade
```
bringen wir das System erst einmal auf den aktuellen Stand.
Als nächstes werden noch einige Pakete installiert.
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
Bevor der Dev-Server installiert werden kann sind folgende Befehle auszuführen (ansonsten kann es zu Fehlern bei den Schreibrechten kommen. !!! Nicht mit sudo installieren !!!):
```
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
nano ~/.profile
```
und im Editor (nano) mit der Cursor-Taste ganz anz Ende scrolen und folgende Zeile anhängen
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

[##Top](#inhaltsverzeichnis)
