---
layout: post
title: Windows-Updates mit PowerShell 
description: >
  Leidige Windows Updates mit PowerShell automatisieren.
image: 
  path: https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/beispiel_artikel.png
  srcset:
    1920w: https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/beispiel_artikel.png
    960w:  https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/beispiel_artikel@0,5.png
    480w:  https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/beispiel_artikel@0,25x.png
sitemap: false
hide_last_modified: true
categories: [article]
tag: [PowerShell]
comments: false
---

* this unordered seed list will be replaced by the toc
{:toc}

Es gibt unzählige Empfehlungen, wie Windows Updates am besten installiert werden sollen. Mein Ich wollte die Updates jedoch nicht einer "Windows Automatik" überlassen, bin aber auch zu faul um auf jedem System die Windows Updates manuell zu installieren. 

Mal abgesehen davon, dass dieses RDP hopping ja auch ziemlich nervt. In größeren Umgebungen wird sicher eine autmatische Softwareverteilung oder ein WSUS die arbeit übernehmen, aber in kleineren Umgebungen ist ein ziemlicher Aufwand diese Systeme zu betreiben und zu verwalten.

Der erste Gedanke war nun, dass mache ich per PowerShell. Und tatsächlich gibt es ein entsprechendes PowerShell Modul in der PowerShell Gallery.

## Arbeiten mit dem PSWindowsUpdate Modul

Nach aktuellem Stand, ist dieses Modul in den Top 10 der [PowerShell Gallery Charts][1]. Wer also seine Windows Updates damit erledigen möchte ist in bester Gesellschaft.

Installiert wird das Modul wie alle anderen Module aus der Powershell Gallery mit dem folgenden Kommando:

~~~powershell
Install-Module -Name PSWindowsUpdate -Force
~~~

Eventuell müsst ihr noch Downloads aus der PowerShell Gallery zulassen. Sollte es bei der Installation zu einem Fehler kommen (gerade bei älteren Systemen), liegt das wahrscheinlich an einer älteren TLS Version. Das folgende Kommando sollte da Abhilfe schaffen:

~~~powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
~~~

Wer das Modul nicht online installieren kann, möchte oder darf, sollte es einfach auf einer Maschine mit Internetzugriff herunterladen. Das geht mit dem folgenden Kommando:

~~~powershell
Save-Module -Name PSWindowsUpdate -Path <Pfad-Zum-Download-Folder>
~~~

Danach muss das Modul auf den Maschinen, auf denen es verwendet werden soll in den folgenden Ordner kopiert werden:

~~~powershell
%WINDIR%\System32\WindowsPowerShell\v1.0\Modules
~~~

Wer es nicht hin und her kopieren möchte, kann es auch Remote auf anderen Maschinen installieren: Das geht mit:

~~~powershell
$ZielMaschinen = "Server1", "Server2", 
Update-WUModule -ComputerName $ZielMaschinen -Local
~~~

## PSWindowsUpdate verwenden

Verwenden können wir das Modul nachdem wir es importiert haben:

~~~powershell
Import-Module PSWindowsUpdate
~~~

Nicht die Execution Policy vergessen, eventuell muss diese zur Ausführung angepasst werden. Habt ihr erhöhte Sicherheit für Powershell konfiguriert, müsst ihr das Modul eventuell noch signieren.
{:.note title="Achtung"}

### Was können wir mit dem Modul nun machen?

Um das rauszubekommen, zeigen wir uns erstmal alle CMDlets an, die wir mit dem Modul verwenden können. Das machen wir mit:

~~~powershell
Get-Command -Module PSWindowsUpdate
~~~

![400x200](/assets/img/article/WUmitPowerShell01.png "Ausgabe von Get-Command -Module PSWindowsUpdate")

Zuerst wollen wir uns mal die für unsere System verfügbaren Updates anschauen. Hier hilft uns das Kommando:

~~~powershell
Get-WUList
~~~

Abhänging wie aktuell euer System ist, wird eine mehr oder weniger lange Liste angezeigt.

~~~
ComputerName Status     KB          Size Title
------------ ------     --          ---- -----
TOMSYOGA900  -D-----    KB5024670  104MB 2023-03 .NET 6.0.15 Update for x64 Client (KB5024670)
TOMSYOGA900  -D-----    KB890830    42MB Windows-Tool zum Entfernen bösartiger Software x64 - v5.111 (KB890830)
TOMSYOGA900  -------    KB5023696  104GB 2023-03 Kumulatives Update für Windows 10 Version 22H2 für x64-basierte Syste…
~~~

Aktuell sind dort noch nicht alle Quellen verfügbar. Wer hier statt Windows Updates auch die Microsoft Updates sehen möchte, muss diese explizit hinzufügen.

~~~powershell
# Anzeigen der Provider
Get-WUServiceManager

# Hinzufügen von Microsoft Updates 
Add-WUServiceManager -ServiceID "7971f918-a847-4430-9279-4a52d1efe18d" -AddServiceFlag 7

# Microsoft Updates auflisten
Get-WUList -MicrosoftUpdate
~~~

Letztendlich wollen wir diese Updates auch installieren. Das machen wir mit dem folgenden Kommando:

~~~powershell
Install-WindowsUpdate -MicrosoftUpdate -AcceptAll -AutoReboot
~~~

Wer nicht möchte, dass die Maschinen einen "AutoReboot" machen, kann auch die folgenden Optionen verwenden:

* IgnoreReboot 
* ScheduleReboot

Wie ich diese Cmdlets in einem Skript verwende, könnt ihr in meinem [Github Account][3] sehen. Ich habe hier ein kleines Skript geschrieben, welches einfach die verfügbaren Updates herunterlädt, installiert und, falls erforderlich, die Maschine neu startet. Das ganze basiert auf einer Textdatei mit den entsprechenden Hostnamen, die nacheinander abgearbeitet werden.

Das PowerShell Module hat auch die Möglichkeit, schnell und simpel einen erforderlichen Reboot abzufragen. Statt mit Skripten in der Registry zu suchen, geht auch der folgende Aufruf:

~~~powershell
Get-WURebootStatus
~~~

Das Kommando gibt **True** zurück, falls noch ein Reboot ansteht. So erspart man sich langes suchen in der Registry.

[1]: https://www.powershellgallery.com/stats/packages
[2]: /assets/img/article/windowsupdates001.png
[3]: https://github.com/thomaskrampe/PowerShell/tree/master/Windows/WindowsUpdate 