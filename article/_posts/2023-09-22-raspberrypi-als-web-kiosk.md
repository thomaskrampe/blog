---
layout: post
title: Raspberry-Pi - Autostart von Chromium im Vollbild
description: >
  Ein Raspberry Pi als Internet Kiosk zur Anzeige von Dashboards, warum nicht.
image: 
  path: https://picsur.kngstn.eu/i/e9a3338e-63da-4d1c-9356-6fcb58ae1fd5.jpg
sitemap: true
hide_last_modified: true
categories: [article]
tag: [Linux]
comments: false
---

* this unordered seed list will be replaced by the toc
{:toc}

Nach einigen Monaten habe ich endlich mal wieder einen Raspi aus meiner IoT Kiste rausgekramt, um ihn als Device zur Anzeige einer Website bzw. eines Dashboards zu verwenden. Ich überspringe mal die Basis Installation mit dem neuesten Raspberry Pi OS (aka. Debian Bullseye) und komme gleich zu dem, was mich etwas aufgehalten hat. 

> Achtung: Es gibt ein [Update][3] in diesem Artikel. Bitte auch dieses lesen.
{:.lead}

## Raspberry Pi als Webkiosk

Einen Raspberry Pi komplett sich selbst zu überlassen und als Webkiosk zu verwenden, setzt einiges an Konfiguration voraus. Fangen wir mal mit dem Autostart des Browsers an. Zuerst muss unter `/etc/xdg/autostart/` eine neue Datei mit der Dateiendung `.desktop` angelegt werden.

~~~shell
sudo vi /etc/xdg/autostart/chromium.desktop
~~~

Folgender Inhalt kommt dann in diese Datei:

~~~console
REM file: 'chromium.desktop'
[Desktop Entry]
Type=Application
Name=Chromium
Comment=Chromium Webbrowser
NoDisplay=false
Exec=chromium-browser --noerrdialogs --disable-crash-reporter --disable-infobars --force-device-scale-factor=1.00 --start-fullscreen "https://www.dashboard.org"
~~~

Die ersten Zeilen sollten selbsterklärend sein, lediglich der Aufruf mit Exec bedarf etwas Erklärung. Zuerst starte ich natürlich den `chromium-browser` und gebe ein paar ausgwählte Command-Line Schalter mit.

| Switch                        | Beschreibung                                       |
|:-----------------------------:|:--------------------------------------------------:|
| `--noerrdialogs`              | Unterbindet eventuelle Fehlermeldungen beim Start  |
| `--disable-crash-reporter`    | Unterbindet den Crash Reporter                     |
| `--disable-infobars`          | Deaktiviert die Infobar                            |
| `--force-device-scale-factor` | Setzt den Scale Faktor wieder auf 1.00             |
| `--start-fullscreen`          | Startet den Browser im Fullscreen                  |

Der letzte Parameter ist dann die Website die gleich mit aufgerufen werden soll.

Ich verwende mit Absicht hier den Parameter `--start-fullscreen` weil ich damit ggf. mit **F11** wieder aus dem Vollbild herauskomme und sich das OS dann weiter nutzen lässt. Wer wirklich einen Kiosk-Mode benötigt, was auch bedeutet, dass ich da eben so einfach nicht mehr herauskomme, kann auch den Parameter `--kiosk` stattdessen verwenden.
{:.note title="Noch eine kleine Anmerkung"}

Einzige Möglichkeit aus dem Kiosk-Mode wieder heraus zu kommen ist eine Verbindung via SSH (wenn aktiviert) und das folgende Kommando.

~~~shell
pkill -o chromium
~~~

Solltet ihr **SSH** nicht beim Basis-Setup konfiguriert haben, hilft eine Neuinstallation der SD-Karte weiter.

Das Kommando mit allen Parametern hinter **Exec=** kann man Testweise auch mal im Terminal eingeben, dort sollte es genauso funktionieren und sich ein Chromium öffnen. So kann man z.B. seine Parameter vorher testen. Aber Achtung, aus dem `--kiosk` Mode kommt man, wie vorher erwähnt, eben so einfach nicht mehr heraus.

Peter Beverloo (danke übrigens für den Aufwand) hat übrigens auf seiner [Website][1] "fast" alle Command-Line Parameter für den Chromium aufgelistet. Lasst euch überraschen.

## Energiesparmodus

Standardmäßig hat der Raspberry ja auch einen Energiesparmodus, diesen benötigen wir hier normalerweise für diesen Einsatzzweck nicht. Um diesen loszuwerden, müssen wir die Datei `/etc/lightdm/lightdm.conf` bearbeiten.

~~~shell
sudo vi /etc/lightdm/lightdm.conf
~~~

Am Ende des Abschnitts **[Seat:*]** (Achtung, nicht direkt unter Seat Configuration) fügen wir die folgende Zeile ein (wir können auch eine vorhande aktivieren und ergänzen).

~~~console
xserver-command=X -s 0 dpms
~~~  

Und damit wären wir auch den Energiespar-Modus los.

## WLAN Power Save

Ja, leider hat der WLAN Controller des Raspberry Pi auch einen Power Save Modus. Wenn eure Netzwerkverbindung per Ethernet Kabel erfolgt, gut. Seid ihr aber über WLAN verbunden, könnte euch so etwas im Log begegnen:

~~~console
{TIMESTAMP} raspberrypi dhcpcd[{PID}]: wlan0: carrier lost
~~~

Wahrscheinlich ist dann der Power Save Mode Schuld daran, dass eure WLAN Verbindung den Dienst einstellt. Ob dieser Modus aktiviert ist, findet ihr mit dem folgenden Kommando heraus:

~~~shell
sudo iw wlan0 get power_safe
~~~~

Wenn die Ausgabe `Power save: off` ist habt ihr Glück gehabt. Andernfalls müssen wir diesen Modus mit dem folgenden Kommando ausschalten:

~~~shell
sudo iw wlan0 set power_save off
~~~~

Damit nach einem Reboot diese Einstallung auch erhalten bleibt, benöigen wir dieses Kommando noch in der Datei `/etc/rc.local`. Direkt vor dem Kommando `exit 0` schreiben wir dann:

~~~shell
sudo vi /etc/rc.local

...
/sbin/iw dev wlan0 set power_save off
exit 0
~~~

Nicht vergessen die Änderungen im Vi mit `:wq!` wieder zu speichern.

## Screen Blanking

Da wir die Desktop-Variante von Raspberry Pi OS verwenden, haben wir das Problem, dass der Bildschirm nach einer bestimmten Zeit der Inaktivität automatisch in den Ruhezustand versetzt wird.

Diese Funktion nennt sich Screen Blanking und muss für unseren Zweck deaktiviert werden. Das geht sowohl grafisch über **Preferences** --> **Raspberry Pi Configuration**. Unter dem Tab **Display** lässt sich dann das Screen Blanking deaktivieren.

Der Weg im Terminal ist ähnlich. Zuerst die Raspberry Konfiguration aufrufen:

~~~shell
sudo raspi-config
~~~  

Danach unter **2 Display Options** --> **D4 Screen Blanking** aufrufen und sicherstellen das hier **\<No\>** ausgewählt ist. Die abschließende Meldung sollte dann bestätigen, dass Screen Blanking deaktiviert ist.

![Screen Blanking deaktivieren](https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/2023-09-22-raspberrypi-als-web-kiosk_01.png)

Eigentlich sollte es das gewesen sein. Ich habe jetzt sicherheitshalber auch noch in der Datei `/etc/xdg/lxsession/LXDE-pi/autostart` den Bildschirmschoner durch auskommentieren der Zeile `@screensaver -no-splash`  und drei zusätzlichen Zeilen deaktiviert (so die Theorie).

~~~shell
sudo vi /etc/xdg/lxsession/LXDE-pi/autostart
~~~

~~~conf
@lxpanel --profile LXDE-pi
@pcmanfm --desktop --profile LXDE-pi
# @xscreensaver -no-splash

# Disable screensaver:
@xset s noblank
@xset s off
@xset -dpms
~~~

## Temperatur

So ein Raspberry kann im laufenden Betrieb ganz schön heiß werden. Es kann also durchaus Sinn machen, die Temperatur so ab und zu zu überwachen. Mit den folgendem kleinen Skript sehen wir die Temperatur der ARM CPU:

~~~shell
cpu=$(</sys/class/thermal/thermal_zone0/temp) && echo "CPU Temperatur ist: $((cpu/1000))C"
CPU Temperatur ist: 60C
~~~

Für die GPU macht ihr das mit dem folgenden Kommando:

~~~shell
vcgencmd measure_temp
~~~

Bis 85 °C ist alles noch ok, danach fängt der Raspberry an, die CPU runter zu regeln, was zu unerwünschten Verhalten führen kann. Wenn euer Raspberry im Betrieb ständig diese Temperaturen erreicht, solltet ihr über einen besseren Kühlkörper bzw. einen Lüfter nachdenken.

## Optional

### Automatischer Reboot

Wenn man jetzt so einen Raspberry dauerhaft laufen lässt, ist auch mal ein Reboot ganz nett. In meinem Fall wird eine Website angezeigt, die die Reservierungen des aktuellen Tages darstellt und sich leider nicht selbst aktualisiert. Aus diesem Grund habe ich einen Cron-Job erstellt, der den Raspi jeden morgen bootet (an dem Refresh der Seite arbeite ich noch, das ist aber eine andere Baustelle).

~~~shell
sudo crontab -e
~~~

Eventuell müsst ihr noch euren bevorzugten Text Editor für das Bearbeiten der crontab Datei auswählen. Ich mache das mit **vi** bzw. mit **vim** (`sudo apt install vim`) aber es steht auf dem Raspi ja auch **nano** zur Verfügung. Die folgende Zeile in der crontab Datei startet den Raspberry Pi jeden morgen um 07:00 Uhr.

~~~shell
0 7 * * * sudo reboot >/dev/null 2>&1
~~~

Wer es etwas komplizierter benötigt, kann sich diese Zeile aber auch Online [generieren][2] lassen.

## Update Oktober 2023

Im Betrieb habe ich festgestellt, dass es doch noch ein paar Dinge gibt die mich stören. Sollte der automatische Reboot aus irgendwelchen Gründen nicht funktionieren, wird der Raspberry bei uns einfach durch Aus- und wieder Einschalten neu gestartet (da im Kiosk Einsatz keine Maus oder Tastatur angeschlossen ist). Das hat zur Folge, dass Chromium eine "abgebrochene" Session in seinen Preference File schreibt und beim nächsten Start mit einer Fehlermeldung startet. Leider muss das manuell bestätigt werden, sonst geht es nicht weiter.

Das zweite Problem ist der Mauszeiger, der immer schön zentriert auf dem Bildschirm angezeigt wird und ehrlich gesagt schrecklich nervt.

Beide Probleme sind relativ einfach zu lösen und ich habe dabei noch einen ganz anderen Weg gefunden, wie das mit dem "Kiosk" auch realisiert werden kann.

### Tools

Um die vorgenannten Probleme zu lösen, brauchen wir zwei zusätzliche Tools. Eines um den Mauszeiger auszublenden und eines um Chrome und das Error Handling zu kontollieren. Fangen wir mit der Installation der Tools an:

~~~shell
sudo apt install unclutter sed
~~~

Das Tool `unclutter` sorgt dafür, dass wir den Mauszeiger ausblenden wenn die Maus nicht verwendet wird. Mit `sed` durchsuchen wir die Chromium-Einstellungsdatei um alle Flags zu löschen, die die Warnleiste erscheinen lassen würden. Genau das, was ein Reboot per Stromkabel auslösen würde.

Falls Chromium jemals abstürzt oder plötzlich geschlossen wird (was auch bei einem Hard-Reboot passiert), stellen wir mit `sed` sicher, dass wir nicht zu Maus und Tastatur greifen müssen, um die Warnleiste zu löschen, die normalerweise dann oben im Browser erscheint.

Die ganzen Anweisungen packen wir jetzt in ein Shell Skript, dass wir mit folgendem Komando anlegen `vi kiosk.sh` (wer lieber nano verwendet dann natürlich `nano kiosk.sh`). Das Skript hat dann folgenden Inhalt:

~~~shell
# file: 'kiosk.sh'
#!/bin/bash

xset s noblank
xset s off
xset -dpms

unclutter -idle 1 -root &

sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' /home/$USER/.config/chromium/Default/Preferences
sed -i 's/"exit_type":"Crashed"/"exit_type":"Normal"/' /home/$USER/.config/chromium/Default/Preferences

/usr/bin/chromium-browser --noerrdialogs --disable-crash-reporter --disable-infobars --force-device-scale-factor=1.00 --start-fullscreen https://www.dashboard.org &

~~~

Statt wie bereits oben im Artikel erwähnt eine chromium.desktop Datei in den Autostart zu legen, führe ich hier später einfach das Shell Skript als Service aus. Aber kommen wir erstmal zum Inhalt:

Die ersten drei `xset` Kommandos habe ich vorher auch bereits verwendet, nur das ich sie jetzt direkt im Skript bei jedem Reboot ausführe. Die ersten beiden xset Kommandos deaktivieren den Bildschirmschoner und die dritte Zeile deaktiviert das **Display Power Management System (dpms)**.

Danach führe ich `unclutter` aus. Diese Anwendung blendet den Mauszeiger aus, wenn er länger als 1 Sekunde inaktiv ist. Der Parameter `-root` entfernt den Mauszeiger auch dann, wenn er sich nicht über dem Browserfenster befindet. Das `&` sorgt nur dafür, dass es im Skript weitergeht.

Den Inaktivitäts-Timer können wir auf die gewünschte Anzahl von Sekunden einstellen, wobei jede Dezimalstelle ein Bruchteil einer Sekunde ist (z.B 1.5 = 1,5 Sekunden usw.).

Zum Schluss verwenden wir `sed` um in der Datei **Preference** des Chromium Browsers nach Flags für eine Warnung zu suchen und diese zu entfernen. Wir suchen hier nach **exited_cleanly:false** und ersetzen das durch **exited_cleanly:true** und nach **exit_type:Crashed** und ersetzen das durch **exit_type:Normal**. Simpel, oder?

Nachdem die Datei *"aufgeräumt"* ist, starten wir dann Chromium mit der gewünschten Website. Wir können hier mehrere Websiten getrennt durch ein Leerzeichen angeben, diese werden dann in Tabs geöffnet. Das Kommando und die Parameter habe ich bereits weiter oben im Artikel erläutert.

Jetzt müssen wir das Skript noch speichern (`:wq` bei vi oder vim `CTRL + X` bei nano) und es ausführbar machen:

~~~shell
chmod u+x ~/kiosk.sh
~~~

### Mehrere Tabs

Wenn wir Chromium mit mehreren Webseiten starten, werden diese, wie bereits erwähnt, in Tabs dargestellt. Allerdings wird im Vollbild bzw. Kiosk Modus nur der erste Tab dargestellt, alle anderen sind hier dummerweise unsichtbar. Wir benötigen also eine Möglichkeit um per emulierten Tastenkommandos, am besten auch zeitgesteuert, durch die geöffneten Tabs zu navigieren.

Dafür gibt es das folgende Tool, das wir wie folgt installieren:

~~~shell
sudo apt install xdotool
~~~

Um jetzt zeitgesteuert durch die geöffneten Tabs zu navigieren, müssen wir nur die folgende Schleife an das Ende unseres `kiosk.sh` Skript schreiben:

~~~shell
while true; do
      xdotool keydown ctrl+Next; xdotool keyup ctrl+Next;
      sleep 15
done
~~~

Die Tastenkombination um durch die Tabs zu navigieren ist CTRL + Page Down. In diesem Fall ist **Next** einfach nur ein Alias für Page Down bzw. PGDN. Was die Schleife macht ist also lediglich CTRL+PG DN zu drücken, das ganze auch wieder loszulassen. Danach geht das Script in einen 15 Sekunden Schlaf um dann wieder von vorn zu beginnen, also hinter dem `do`. Deshalb ist es wichtig, die Schleife am Ende des Skript zu patzieren, denn es ist eine sogenannte Endlosschleife und es würden nach dem `done` keine weiteren Kommandos mehr ausgeführt werden.

Alternativ würde auch die Tastenkombination CTRL + r gehen, das würde einen Refresh der Seite durchführen.
{:.note title="Kleiner Tip"}

### Der neue Autostart

Statt das ganze per chromium.desktop Datei in den Autostart zu legen, erstelle ich jetzt einen Service. Dazu müssen wir einen `.service` File mit folgendem Inhalt erstellen:

~~~console
sudo vi /lib/systemd/system/kiosk.service
~~~

**Inhalt:**

~~~console
REM file: 'kiosk.service'
[Unit]
Description=Chromium Kiosk
Wants=graphical.target
After=graphical.target

[Service]
Environment=DISPLAY=:0.0
Environment=XAUTHORITY=/home/thomas/.Xauthority
Type=simple
ExecStart=/bin/bash /home/thomas/kiosk.sh
Restart=on-abort
User=thomas
Group=thomas

[Install]
WantedBy=graphical.target
~~~

Interessant sind hier nur die Zeilen unter **[Service]**, alles andere einfach so übernehmen. Wenn ihr nur einen Bildschirm angeschlossen habt, sollte der Parameter `DISPLAY=:0.0` passen. Habt ihr jedoch mehrere Displays angeschlossen, müssen wir mit `echo $DISPLAY` erst noch das entsprechende Display ermitteln und dann entsprechend hinter `DISPLAY=` eintragen.

In dem Beispiel wird auch überall der Benutzer **thomas** mit der Gruppe **thomas** verwendet. Das muss natürlich durch euren Benutzer (auch in den Pfaden) ersetzt werden. Danach die Datei einfach speichern.

Jetzt müssen wir dem System natürlich den neuen Service noch bekannt machen, das erledigen wir mit dem folgenden Kommando:

~~~shell
sudo systemctl enable kiosk.service
~~~

Damit wird der "Service" jetzt bei jedem Reboot das Raspberry Pi gestartet. Um das zu testen, könnt ihr natürlich den Raspberry rebooten oder ihr startet den Dienst manuell mit dem folgenden Kommando:

~~~shell
sudo systemctl start kiosk.service
~~~

Wenn ihr den Zustand des Service überprüfen wollt, ganz besonders dann, wenn der Service nicht startet hilft das folgenden Kommando:

~~~shell
sudo systemctl status kiosk.service
~~~

Das Ergebniss sollte in etwa das folgende Anzeigen:

~~~console
Loaded: loaded ...
Active: active (running) ...

...
~~~

Zusätzlich gibt es dann noch Informationen zur CPU Nutzung etc., oder es steht ein entsprechender Fehler in der Augsgabe. Diesen müsst ihr dann zuerst beheben. Meist sind es aber nur Tippfehler oder einer der Pfade stimmt auf eurem System nicht so wie in meinem Beispiel.

Natürlich lässt sich der Service auch stoppen:

~~~shell
sudo systemctl stop kiosk.service
~~~

Ich hoffe ihr habt den Artikel bis zum Ende gelesen. Den Update Teil finde ich deutlich besser und habe das auch so implemetiert. Natürlich könnt ihr auch beim ersten Teil bleiben, ihr müsst nur sicherstellen, dass ihr die unclutter und sed Kommandos vor dem Start von Chrome ausführt. Ich würde in dem Fall auch das hier beschrieben kiosk.sh Skript nehmen und es in der Datei `/etc/xdg/autostart/chromium.desktop` mit der Anweisung Exec ausführen. Also statt ...

~~~console
Exec=chromium-browser --noerrdialogs --disable-crash-reporter --disable-infobars --force-device-scale-factor=1.00 --start-fullscreen "https://www.dashboard.org"
~~~

...würde ich dann folgendes verwenden:

~~~console
Exec=/bin/bash /home/thomas/kiosk.sh
~~~

Aber das überlasse ich euch.

Das sollte im Wesentlichen alles gewesen sein. Falls ich noch etwas zusätzlich finde, werde ich es gern hier in diesem Artikel ergänzen. Schreibt mir gern, wenn ihr eine bessere oder zusätzliche Lösung habt.

[1]: https://peter.sh/experiments/chromium-command-line-switches/
[2]: https://crontab-generator.org/
[3]: https://blog.kngstn.eu/article/2023-09-22-raspberrypi-als-web-kiosk/#update-oktober-2023
