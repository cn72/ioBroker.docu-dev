# ioBroker.docu-dev-install

## Inhaltsverzeichnis
1. [Einleitung](#einleitung)
2. [Verweise](#verweise)
3. [Entwicklungsumgebung](#entwicklungsumgebung)
4. [Adapter](#adapter)
5. [Visual Studio Code](#visual-studio-code)
6. [Backgrounds](#backgrounds)
7. [Adapter Creator](#adapter-creator)

## Einleitung
Diese Dokumentation richtet sich an alle Interessierte, die eine Erweiterung (Adapter) für den ioBroker programmieren / entwickeln wollen.
Viele befassen sich mit der SmartHome Technik des ioBroker's als Hobby und der ein oder andere steigt tiefer in die Materie ein. Hier soll ein Grundgerüst an die Hand gegeben werden,
mit dem es auch mit wenig Vorkenntnissen möglich sein wird, einen Adapter zu erstellen. Diese Dokumentation soll etwas Licht ins Dunkle bringen,
kann aber natürlich nicht alle Möglichkeiten abbilden (bei weitem nicht), die es bei der Erstellung von Funktionen eines Adapters gibt
Aber zur Erlangung von Grundkenntnissen soll diese Dokumentation ein guter Einstieg sein.

> [!NOTE]
> Letzte Änderung: 01.01.2025

[Top](#inhaltsverzeichnis)

## Verweise
Hier gibt es eine kleine  Auflistung, wo man noch an Infos herankommt, welche von Interesse sein könnten.

- [Youtube Video von haus:automation](https://www.youtube.com/watch?v=A9UETXyAmL4) diente u.a. als Leitfaden für diese Dokumentation
- [ioBroker.dev Entwicklungsseite](https://www.iobroker.dev/) Hinweis : Login nur mit GitHub Account !!!
- [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) inkl.PuttyGen
- [GitHub](https://github.com/) Account erstellen
- [NPM](https://www.npmjs.com/) Account erstellen
- [Visual Studio Code](https://code.visualstudio.com/) Editor mit Entwicklungsspezifischen Erweiterungsmöglichkeiten

> [!NOTE]
> Letzte Änderung: 01.01.2025

[Top](#inhaltsverzeichnis)

## Entwicklungsumgebung

Für die Entwicklung von Adaptern ist es mehr als sinvoll, eine eigene Entwicklungsumgebung zu nutzen, um sein "Live-System" nicht zu beschädigen,
bzw. sein Entwicklungssystem gleich so einzurichten, um eine Verbindung zu GitHub und den ioBroker Repository herzustellen, damit Updates etc.
ohne großen Aufwand veröffentlicht werden können. Solch ein Entwicklungsumgebung kann auf verschiedenen Systemen installiert werden. Es gibt halt 1000 Wege,
die nach Rom führen. Ob ein eigener Rechner verwendet wird, oder ob man ein Linuxsystem auf einem virtuellen System aufsetzt oder ob für den DEV-Server
einem Windowsrechner verwendet wird ist prinzipiell egal. Da viele ja ioBroker bereits am laufen haben und eventuell auf eine neuere/stärkere Hardware umgestiegen sind hat der ein oder andere noch einen Raspberry in der Schublade liegen.
Ich habe mich hier für einen Raspberry Pi 4 entschieden - als Rechner für den DEV-Server und als Entwicklungs-Editor den "VisualCode Studio" Editor
auf einem Windows Rechner. Mit Hilfe des Raspberry Pi Imager wird das OS ohne Desktop installiert.
Beim Imager wird als OS "Raspberry Pi OS (other)" und dann "Raspberry Pi OS Lite (64bit)" ausgewählt. Nach Auswahl der SD-Karte hat man die Möglichkeit,
die Einstellungen anzupassen. Folgende Einstellungen habe ich hier vorgenommen:

* Tab Allgemein: 
  
  - Hostname-> iodev.local
  - Benutzername -> pi
  - Passwort-> raspberry
  - WIFI-> das am Standort verwendete und unter Passwort das WLAN Passwort eingeben
  - WIFI Land-> DE
  - Zeitzone->Europe/Berlin
  - Tastaturlayout-> de
* Tab Dienste:
  - SSH -> aktivieren
  - Passwort zur Authentifizierung verwenden->auwählen
  
Danach speichern und danach 2x mit ja bestätigen.
Nach ein paar Minuten ist das Image auf die SD-Karte geschrieben und kann in den Pi4 eingesetzt werden. Jetzt noch Strom und der Pi4 startet und sollte sich mit dem lokalen WLan verbinden. Am Router kann die IP-Adresse ermittelt werden.
Um auf den Raspberry zugreifen zu können wird ein Terminal-Programm verwendet. Hier verwende ich Putty.

Wir melden uns als Benutzer: pi an. Hat man zum Beispiel ein Linuxsystem auf einer virtuellen Umgebung installiert, bitte einen Benutzer anlegen und sudo installieren / Benutzer zuordnen und dann mit diesem Benutzer arbeiten.


Mit:
```
sudo apt-get update
sudo apt-get upgrade
```
bringen wir das System erst einmal auf den aktuellen Stand.
Als nächstes werden noch einige Pakete installiert.
```
sudo apt install -y git vim net-tools curl gcc g++ make
```
danach installieren wir node 

> [!CAUTION]
>bitte schauen, welche Version aktuell ist und die 20 durch die aktuelle Version ersetzen
>
>Stand hier: 01.01.2025
```
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
```
und danach

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

> [!NOTE]
> Letzte Änderung: 01.01.2025

[Top](#inhaltsverzeichnis)

## Adapter

Der Speicherbereich auf dem Entwicklungssystem liegt im Home Verzeichnis des Benutzers, den wir bei der Installation des Dev-Servers verwendet haben. Unter Linux ist das Homeverzeichnis mit
```
cd ~
````
zu erreichen.

Dort können nun die einzelnen Adapter, die man erstellt bzw. an denen man arbeitet hinterlegt werden. Dabei ist zu beachten, dass jedes einzelne Adapterverzeichnis eine eigene Instanz des Dev-Servers sein wird.

Die Adapterverzeichnisse haben alle die Bezeichnung
```
ioBroker.adaptername
```
Gerade für Neulinge bietet es sich an, in GitHub aus dem bestehenden Adaptern diese als Kopie (fork) in das lokale Verzeichnis anzulegen und dann zu schauen, wie was gemacht wurde und was passiert wenn zu "spielen" (learning by doing)
Die offizellen ioBroker Repositories sind unter

[https://github.com/orgs/ioBroker/repositories](https://github.com/orgs/ioBroker/repositories)

zu finden.

Dort kann man dann einen bestehenden Adapter auswählen und einen Fork als eigenes Repository erstellen.
Durch den Fork hat man die Möglichkeit behalten, evtl. Änderungen via Pull Requests zurück in das Hauptrepository zu senden.
Den Code aus dem eigenen GitHub Account lädt man nun direkt aus Github herunter und hat so eine Verbindung vom Dev-Server zu Github. Den "Downloadlink" bekommt man, wenn man in das Repository geht und auf der Seite den grünen Button "Code<>" anklickt und dort aus dem Tab SSH den "Link" kopiert. Über Putty meldet man sich nun am Raspberry an und gibt
```
git clone "hier dann den Link ohne Anführungsstriche"
```
ein.

Darauf hin wird in das Homevereichnis der Adapter kopiert.
Jetzt muss noch das Verzeichnis aufgerufen und der DEV-Sever installiert werden.
```
cd ioBroker.adaptername
dev-sever setup
```
Die Installation des Servers dauert etwas. Nach der Installation startet man den Server durch
```
dev-server watch
```
nach einiger Zeit ist der Server über den Browser mit der IP Adresse auf dem Port 8081 erreichbar. Die Seiten sollten jedem bekannt vorkommen.
Durch STRG+c beendet man den Server.

Einen neuen Adapter erstellt man am besten mit dem [Adapter Creator](#adapter-creator).

> [!NOTE]
> Letzte Änderung: 02.01.2025

[Top](#inhaltsverzeichnis)

## Visual Studio Code

Als Editor eignet sich prinzipiell jeder Texteditor. Der Visual Studio Code (VSC) Editor zeichnet sich u.a. dadurch aus, dass es für viele Sprachen und Funktionen wie Git  Erweiterungen gibt, um sich die Arbeit leichter gestalten zu können. Die Installation des Editor sollte kein Problem darstellen.

Als Erweiterung (Extension) installieren wir Remote - SSH von Microsoft, damit wir direkt auf den Speicherort auf dem Raspberry zugreifen können.

Um auch hier nicht jedesmal das Passwort für den Raspberry eingeben müssen, erstellen wir uns am Windowsrechner auch einen SSH-Key. Dazu öffnen wir die Konsole am Windowsrechner (cmd) als Administrator und geben
```
ssh-keygen -t ed25519 -C "Kommentar/Bezeichnung des Rechners/ist Optional"
```
und bestätigen die Fragen einfach mit Enter.
Jetzt wird im Buntzerverzeichnis im Ordner .ssh der Private und Public Key gespeichert. Den Public Key speichern wir beim Entwicklungsrechner (Raspberry) in der Datei authorized_keys. (Dazu die Datei auf dem Windowsrechner mit einem Editor öffnen und den Key in die Zwischenablage kopieren.)
```
nano ~/.ssh/authorized_keys
```
dort mit der Cursor down Taste ans Ende scrollen. ssh-rsa voranstellen und mit Maus Rechtsklick den Key einfügen. Eventuelle Zeilenumbrüche müssen entfernt werden. Mit STRG+C , Y und Enter speichern.
Beispiel, wie es aussehen soll:
```
ssh-rsa 1lZDI1NTE5AAAAIGUxrbUG86I+......jPg9LdpuwMEJVgJiZ6iNeOBRaAkvoX8u Laptop Win11
```
nun können wir beim VCS Editor unten links die blauen Pfeile anklicken und geben oben
```
ssh pi@ipadresse_raspberry
```
ein. Nach Bestätigung (fingerprint) müssen wir nun das Verzeichnis auswählen, in dem der Adapter Quellcode liegt, den wir bearbeiten wollen.
Wenn alles richtig gemacht wurde wird am linken Rand die Verzeichnisstrucktur angezeigt.
Wir können nun die einzelnen Dateien bearbeiten. Mit STRG+S wird die Datei gespeichert. Der VSC-Editor erkennt, dass es sich um ein Github Verzeichnis handelt und wir können direkt vom Editor unsere Änderungen "Comitten" (Message nicht vergessen) und die Änderungen werden nach github hochgeladen.

Wenn wir den DEV-Server am Raspberry für den Adapter getartet haben, werden die gemachten Änderungen direkt übernommen und wir können das Ergebnis am Browser sehen.

Man kann sich auch direkt aus dem VCS-Editor am raspberry via Terminal anmelden. Geht am schnellsten durch STRG+Ö.

> [!NOTE]
> Letzte Änderung: 02.01.2025

[Top](#inhaltsverzeichnis)

## Backgrounds

Bevor wir uns jetzt an die Erstellung eines eigenen Adapters mit Hilfe des Adapter Creators machen, werden ich hier einiges an Hintergrundwissen zusammenstellen. Dabei hilft es ungemein sich wie oben beschrieben, bestehende Adapter im Aufbau anzuschauen und möglichst auch versuchen zu verstehen, wieso welche Funktion verwendet wird.

Ich erklären hier nicht die einzelnen Dinge, sondern verweisen auf Seiten, wo dies möglichst verständlich erklärt wird.

Ich verweisen auch auf nicht mehr gültige Dinge (wegen Historie, wie es vorher gemacht wurde), da diese noch bei einigen Adaptern auftauchen, und man dann die Zusammenhänge besser erkennen kann.

| Bescheibung | Link | Anmerkung |
|---|---|---|
| offizielle Dokuseite |[GitHub](https://github.com/ioBroker/ioBroker.docs) | english |
||[Github](https://github.com/ioBroker/ioBroker.docs/tree/master/docs/de)|deutsch|
|Adapter Entwicklung<br>offizielle Dokuseite|[Github](https://github.com/ioBroker/ioBroker.docs/tree/master/docs/en/dev)|english|
||[Github](https://github.com/ioBroker/ioBroker.docs/tree/master/docs/de/dev)|deutsch|
|adapter-dev Beschreibung,<br>Translate Funktion|[Github](https://github.com/ioBroker/adapter-dev/blob/main/README.md)|english
|Historie: Translate Funktion<br>mit Gulp|[Forum ioBroker](https://forum.iobroker.net/topic/19047/gulp-ist-kein-hexenwerk-auto-translation)|verweis Forum|
|Adapter-Core|[Github](https://github.com/ioBroker/adapter-core/blob/master/README.md)|english|
|Aufbau der jsonConfig.json Datei<br>für das Admin Panel eines<br>Adapters|[Github](https://github.com/ioBroker/ioBroker.admin/blob/v6.11.0/src/src/components/JsonConfigComponent/SCHEMA.md)|english|
|dto.neuere Version|[Github](https://github.com/ioBroker/ioBroker.admin/blob/v7.0.1/packages/jsonConfig/SCHEMA.md)|english|
|Demo-Adapter<br>zur jsonConfig.json|[Github](https://github.com/mcm4iob/ioBroker.jsonconfig-demo)|entweder per GitHub installieren<br>oder forken (s.o.)<br>oder einfach nur studieren|
|Infos zur<br>Adapter Konfiguration<br>via reactJS|[Github](https://github.com/ioBroker/adapter-react-v5/blob/main/README.md)|english|


> [!NOTE]
> Letzte Änderung: 03.01.2025

[Top](#inhaltsverzeichnis)


## Adapter Creator

[Top](#inhaltsverzeichnis)
