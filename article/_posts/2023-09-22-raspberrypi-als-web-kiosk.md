---
layout: post
title: Raspberry-Pi - Autostart von Chromium im Vollbild
description: >
  Ein Raspberry Pi als Internet Kiosk zur Anzeige von Dashboards, warum nicht.
image: 
  path: https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/2023-09-22-raspberrypi-als-web-kiosk_1280.png
sitemap: true
hide_last_modified: true
categories: [article]
tag: [Linux]
comments: false
---

* this unordered seed list will be replaced by the toc
{:toc}

Nach einigen Monaten habe ich endlich mal wieder einen Raspi aus meiner IoT Kiste rausgekramt, um ihn als Device zur Anzeige einer Website bzw. eines Dashboards zu verwenden. Ich überspringe mal die Basis Installation mit dem neuesten Raspberry Pi OS (aka. Debian Bullseye) und komme gleich zu dem, was mich etwas aufgehalten hat.

## Raspberry Pi als Webkiosk

Einen Raspberry Pi komplett sich selbst zu überlassen und als Webkiosk zu verwenden, setzt einiges an Konfiguration voraus. Fangen wir mal mit dem Autostart des Browsers an. Zuerst muss unter `/etc/xdg/autostart/` eine neue Datei mit der Dateiendung `.desktop` angelegt werden.

~~~console
sudo vi /etc/xdg/autostart/chromium.desktop
~~~

Folgender Inhalt kommt dann in diese Datei:

~~~console
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

~~~console
pkill -o chromium
~~~

Solltet ihr **SSH** nicht beim Basis-Setup konfiguriert haben, hilft eine Neuinstallation der SD-Karte weiter.

Das Kommando mit allen Parametern hinter **Exec=** kann man Testweise auch mal im Terminal eingeben, dort sollte es genauso funktionieren und sich ein Chromium öffnen. So kann man z.B. seine Parameter vorher testen. Aber Achtung, aus dem `--kiosk` Mode kommt man, wie vorher erwähnt, eben so einfach nicht mehr heraus.

Peter Beverloo (danke übrigens für den Aufwand) hat übrigens auf seiner [Website][1] "fast" alle Command-Line Paramter für den Chromium aufgelistet. Lasst euch überraschen.

## Energiesparmodus

Standardmäßig hat der Raspberry ja auch einen Energiesparmodus, diesen benötigen wir hier normalerweise nicht. Um diesen loszuwerden, müssen wir die Datei `/etc/lightdm/lightdm.conf` bearbeiten.

~~~console
sudo vi /etc/lightdm/lightdm.conf
~~~

Am Ende des Abschnitts **[Seat:*]** (Achtung, nicht direkt unter Seat Configuration) fügen wir die folgende Zeile ein (wir können auch eine vorhande aktivieren und ergänzen).

~~~console
xserver-command=X -s 0 dpms
~~~  

Und damit wären wir auch den Energiespar-Modus los.

## Screen Blanking

Da wir die Desktop-Variante von Raspberry Pi OS verwenden, haben wir das Problem, dass der Bildschirm nach einer bestimmten Zeit der Inaktivität automatisch in den Ruhezustand versetzt wird.

Diese Funktion nennt sich Screen Blanking und muss für unseren Zweck deaktiviert werden. Das geht sowohl grafisch über **Preferences** --> **Raspberry Pi Configuration**. Unter dem Tab **Display** lässt sich dann das Screen Blanking dann deaktivieren.

Der Weg im Terminal ist ähnlich. Zuerst die Raspberry Konfiguration aufrufen:

~~~console
sudo raspi-config
~~~  

Danach unter **2 Display Options** --> **D4 Screen Blanking** aufrufen und sicherstellen das hier No ausgewählt ist. Die abschließende Meldung sollte dann bestätigen, dass Screen Blanking deaktiviert ist.

![Screen Blanking deaktivieren](https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/2023-09-22-raspberrypi-als-web-kiosk_01.png)

Eigentlich sollte es das gewesen sein. Ich habe jetzt sicherheitshalber auch noch in der Datei `/etc/xdg/lxsession/LXDE-pi/autostart` den Bildschirmschoner durch auskommentieren der Zeile `@screensaver -no-splash`  und drei zusätzlichen Zeilen deaktiviert (so die Theorie).

~~~console
sudo vi /etc/xdg/lxsession/LXDE-pi/autostart
~~~

~~~console
@lxpanel --profile LXDE-pi
@pcmanfm --desktop --profile LXDE-pi
# @xscreensaver -no-splash

# Disable screensaver:
@xset s noblank
@xset s off
@xset -dpms
~~~

## Optional

Wenn man jetzt so einen Raspberry dauerhaft laufen lässt, ist auch mal ein Reboot ganz nett. In meinem Fall wird eine Website angezeigt, die die Reservierungen des aktuellen Tages darstellt und sich leider nicht selbst aktualisiert. Aus diesem Grund habe ich einen Cron-Job erstellt, der den Raspi jeden morgen bootet (an dem Refresh der Seite arbeite ich noch, das ist aber eine andere Baustelle).

~~~console
sudo crontab -e
~~~

Eventuell müsst ihr noch euren bevorzugten Text Editor für das bearbeiten der crontab Datei auswählen. Ich mache das mit **Vi** aber es steht auf dem Raspi ja auch **nano** zur Verfügung. Die folgende Zeile in der crontab Datei startet den RaspberryPi jeden morgen um 07:00 Uhr.

~~~console
0 7 * * * sudo reboot >/dev/null 2>&1
~~~

Wer es etwas komplizierter benötigt, kann sich diese Zeile aber auch Online [generieren][2] lassen.

Das sollte im Wesentlichen alles sein. Schreibt mir gern wenn ihr eine bessere Lösung habt.

[1]: https://peter.sh/experiments/chromium-command-line-switches/
[2]: https://crontab-generator.org/
