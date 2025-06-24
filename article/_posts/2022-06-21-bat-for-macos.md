---
layout: post
title: CAT Alternative für macOS
description: >
  Wer viel mit Skripten im Terminal arbeitet, wird cat in schön zu schätzen wissen.
image: 
  path: https://picsur.kngstn.eu/i/d9f16743-a851-485a-9165-c01e65d01296.jpeg
sitemap: true
hide_last_modified: true
categories: [article]
tag: [macOS]
comments: false
---

* this unordered seed list will be replaced by the toc
{:toc}

Wer viel mit dem Terminal unter macOS arbeitet, kennt natürlich auch die wichtigsten Kommandos. Eines davon ist **cat** zum Anzeigen von Textdateien. Der Syntax von **cat** ist dabei relativ einfach:

~~~console
cat /pfad/file.txt
~~~

Mehr über die Optionen für **cat** erfahrt ihr mit `man cat`.

Für einfache Textdateien ist das auch völlig ausreichend. Möchte man sich jedoch Skripte oder ähnlich anzeigen lassen, wird das ganze etwas unübersichtlich.

![Ausgabe von cat][1]

Nun könnte man die Datei auch mit nano, vim oder vi öffnen und wenn wir dort Syntax Highlighting konfiguriert haben, geht das auch „in Farbe“. Einfacher macht es ein kleines Zusatztool mit dem schönen Namen **bat**. Der Syntax ist dabei ähnlich wie auch bei **cat**:

~~~console
bat /pfad/file.txt
~~~

![Ausgabe von bat][2]

Unter manchen Linux Systemen z.B. Ubuntu heißt das Tool **batcat** /pfad/file.txt, da dort meistens bereits ein anderes Tool mit dem Namen BAT existiert.
{:.note title="Achtung"}

Die Installation erfolgt auf macOS völlig unkompliziert über [Homebrew][6] mit dem folgenden Kommando:

~~~console
brew install bat
~~~

Übrigens unter Windows könnt ihr BAT auch nutzen und einfach mit [Chocolatey][7] installieren:

~~~console
choco install bat
~~~

![bat unter Windows][3]

Aber BAT kann noch viel mehr neben der Unterstützung für andere Kommandos wie z.B DIFF, FIND oder TAIL wird auch GIT unterstützt.

Mehr Information zu BAT erhaltet ihr direkt im [Github Repository][4] des Entwicklerteams. Vielen Dank an [David Peter][5] und dem ganzen Entwicklerteam

[1]: https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/CAT_01.png
[2]: https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/CAT_02.png
[3]: https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/CAT_03.png
[4]: https://github.com/sharkdp/bat
[5]: https://david-peter.de/
[6]: https://blog.kngstn.eu/article/2022-05-15-homebrew/
[7]: https://chocolatey.org/install
