# ioBroker.docu-dev

## Inhaltsverzeichnis
1. [Einleitung](#einleitung)
2. [Verweise](#verweise)
3. [Entwicklungsumgebung](#entwicklungsumgebung)

## Einleitung
Diese Docu richtet sich an alle Interessierte, die eine Erweiterung (Adapter) für den ioBroker programmieren / entwickeln wollen.
Viele befassen sich mit der SmartHome Technik des ioBroker's als Hobby und der ein oder andere steigt tiefer in die Materie ein. Hier soll ein Grundgerüst an die Hand gegeben werden,
mit dem es auch mit wenig Vorkenntnissen möglich sein wird, einen Adapter zu erstellen. Diese Anleitung soll etwas Licht ins Dunkle bringen,
kann aber natürlich nicht alle Möglichkeiten abbilden (bei weitem nicht), die es bei der Erstellung von Funktionen eines Adapters gibt.
Aber zur Erlangung von Grundkenntnissen ist diese Anleitung ein guter Einstieg.

[Top](#inhaltsverzeichnis)

## Verweise
Hier gibt es eine kleine  Auflistung, wo man noch an Infos herankommt, welche von Interesse sein könnten, bzw. Lücken im Hintergrundwissen stopfen...;)

- [ioBroker.dev Entwicklungsseite](https://www.iobroker.dev/) Hinweis : Login nur mit GitHub Account !!!
- [Putty](https://www.putty.org/) inkl.PuttyGen
- [GitHub](https://github.com/) Account erstellen
- [NPM](https://www.npmjs.com/) Account erstellen
- [Visual Studio Code](https://code.visualstudio.com/) Editor mit Entwicklungsspezifischen Erweiterungsmöglichkeiten

  [Top](#inhaltsverzeichnis)

## Entwicklungsumgebung

Für die Entwicklung von Adaptern ist es mehr als sinvoll, eine eigene Entwicklungsumgebung zu nutzen, um sein "Live-System" nicht zu beschädigen,
bzw. sein Entwicklungssystem gleich so einzurichten, um eine Verbindung zu GitHub und den ioBroker Reposity herzustellen, damit Updates etc.
ohne großen Aufwand veröffentlicht werden können. Solch ein Entwicklungsumgebung kann auf verschiedenen Systemen installiert werden. Es gibt halt 1000 Wege,
die nach Rom führen. Ob ein eigener Rechner verwendet wird, oder ob man ein Linuxsystem auf einem virtuellen System aufsetzt oder ob für den DEV-Server
einem Windowsrechner verwendet wird ist prinzipiell egal. Da viele ja ioBroker bereits am laufen haben und sich irgendwie da schon Gedanken zu gemacht
haben ist oftmals der ein oder andere mit einem Raspberry angefangen und hat dadurch noch den ein oder anderen Raspberry in der Schublade liegen.
Ich habe mich hier für einen Raspberry Pi 4 entschieden - als Rechner für den DEV-Server und als Entwicklungs-Editor den "VisualCode Studio" Editor
auf einem Windows Rechner. Mit Hilfe des Raspberry Pi Imager wird das OS ohne Desktop installiert.
Beim Imager wird als OS "Raspberry Pi OS (other)" und dann "Raspberry Pi OS Lite (64bit)" ausgewählt. Nach Auswahl der SD-Karte hat man die Möglichkeit,
die Einstellungen anzupassen. Folgende Einstellungen habe ich hier vorgenommen:
Tab Allgemein: Hostname-> iodev.local; Benutzername -> pi; Passwort-> raspberry; WIFI-> das am Standort verwendete und unter Passwort das WLAN Passwort eingeben;WIFI Land-> DE;Zeitzone->Europe/Berlin; Tastaturlayout-> de
Tab Dienste: SSH -> aktivieren; Passwort zur Authentifizierung verwenden->auwählen
Danach speichern und danach 2x mit ja bestätigen.
Nach ein paar Minuten ist das Image auf die SD-Karte geschrieben und kann in den Pi4 eingesetzt werden. Jetzt noch Strom und der Pi4 startet und sollte sich mit dem lokalen WLan verbinden. Am Router kann die IP-Adresse ermittelt werden.
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

Stand hier: 01.01.2025
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
und im Editor (nano) mit der Cursor-Taste ganz ans Ende scrolen und folgende Zeile anhängen
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

Um beim Anmelden am Raspberry nicht jedesmal den Benutzer und Passwort eingeben zu müssen (wird für die Arbeit mit VisualStudio Code interessant) erstellen wir uns einen ssh key und nutzen diesen für die Anmeldung.
Am Windows Rechner (oder dem System, über das wir und via Putty mit dem Raspberry verbinden) erstellen wir mit Hilfe von PuttyGen einen Privaten und öffentlichen SSH-Key.
Den öffentlichen Key kopieren wir zum Raspberry, indem wir folgendes tun
```
mkdir ~/.ssh
nano ~/.ssh/authorized_keys
```
dort dan den öffentlichen Key hinein kopieren (begin mit ssh-rsa)
und mit Strg-X->y->Enter speichern und schließen
danach
```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```
am einfachsten ist es, das System danach neu zu starten
```
sudo reboot
```
Am Rechner mit Putty müssen wir nun den Privaten Key hinterlegen.
Dabei die abgespeicherte Datei mit dem privaten Key (die mit der Endung .ppk) bei Putty im linken Baum unter "Connection"->SSH->Auth->Credentials und dann im Feld "Private key file for authentication" hinterlegen.
Auf der Session Seite unter Host Name den Benutzer und die IP hinterlegen (im Feld Host Name): benutzer@ipadresse
Nach dem Speichern sollte man sich am Raspberry via SSH anmelden können, ohne ein Passwort eingeben zu müssen.

Nun wollen wir unser Entwicklungssystem noch mit unserem GitHub Account verbinden. Wer noch keinen GitHub Account hat, sollte dies nun nachholen.
Wir benötigen einen Key, damit der Rechner die Daten zu GitHub senden und empfangen kann. Hier verwenden wir die Verschlüsselungstechnik ed25519. Um eine bessere Übersicht bei den Key's zu haben nutzen wir das Flag -C und fügen dein Kommntar ein, also
```
ssh-keygen -t ed25519 -C "Mein Kommentar"
ssh-add -K ~/.ssh/id_ed25519
```
der Key wird im Verzeichnis ~/.ssh abgelegt und kann mit
```
cat ~/.ssh/id_ed25519.pub
```
angezeigt werden. Diesen Key geben wir in GitHub in unsere "Settings" (oben rechts auf das Avatar klicken, unten dann Settings) unter "SSH and GPG Keys" ein. 
Jetzt konfigurieren wir noch unsere git Einstellungen am Rechner, damit wir puschen und pullen können
```
git config --global user.name "Dein Name"
git config --global user.email "DeineE-Mail@domain.com"
```


[Top](#inhaltsverzeichnis)
