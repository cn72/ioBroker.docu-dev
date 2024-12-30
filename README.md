# ioBroker.docu-dev

## Inhaltsverzeichnis
1. Entwicklungsumgebeung

## Entwicklungsumgebeung

Für die Entwicklung von Adaptern ist es mehr als sinvoll, eine eigene Entwicklungsumgebung zu nutzen, um sich ein "Live-System" nicht zu beschädigen, bzw. sein Entwicklungssystem gleich so einrichten zu können, um eine Verbindung zu GitHub und den ioBroker Reposity herzustellen, damit Updates etc. ohne großen Aufwand veröffentlicht werden können. Solch ein Entwicklungsumgebung kann auf verschiedenen Systemen installiert werden. Es gibt halt 1000 Wege, die nach Rom führen. Ich habe mich hier für einen Raspberry Pi 4 entschieden als Rechner für den DEV-Server und als Entwicklungs-Editor VisualCode Studio auf einem Windows Rechner. Installationssystem ist ein RaspberryPi 4 mit einer 16GB SD-Karte. Mit Hilfe des Raspberry Pi Imager wird das OS ohne Desktop installiert.
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
