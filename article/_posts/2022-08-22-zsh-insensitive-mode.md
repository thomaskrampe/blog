---
layout: post
title: ZSH Insensitive Mode
description: >
  ZSH und der ls - Befehl zum Auflisten von Dateien ohne Berücksichtigung der Groß- und Kleinschreibung
image: 
  path: https://picsur.kngstn.eu/i/72be45ee-3fc7-40cf-ab03-690f68b58f5b.png
sitemap: true
hide_last_modified: true
categories: [article]
tag: [macOS]
comments: false
---

Dateien mit LS auflisten, die einem bestimmem Schema entsprechen machen wir ja eigentlich relativ einfach mit dem folgenden Kommando:

~~~console
ls -l *quotes*
~~~~

In meinem Fall ist das Ergebnis lediglich eine Datei:

~~~console
thomas@Toms-MBP Documents % ls -l *quotes*                            
-rw-r--r--@ 1 thomas  staff  2306 Jul 24 11:49 quotes.txt
~~~~

Mac OS und die ZSH sind aber "case sensitive", das heißt hier wird die Groß- bzw. Kleinschreibung berücksichtigt. Dieses Verhalten können wir mit dem folgenden Kommando abschalten:

~~~console
unsetopt CAse_glob
~~~~

Das Ergebnis danach sieht dann wie folgt aus:

~~~console
thomas@Toms-MBP Documents % unsetopt CAse_glob
thomas@Toms-MBP Documents % ls -l *quotes*    
-rw-r--r--@ 1 thomas  staff   899 Jun 28 11:41 denglish_Quotes.txt
-rw-r--r--@ 1 thomas  staff  2306 Jul 24 11:49 quotes.txt
~~~~

Da jetzt die ZSH nicht mehr "case sensitive" ist, werden alle Dateien angezeigt die dem Suchmuster entsprechen. Natürlich berücksichtigt auch das Kommando `find` diese Einstellung:

~~~console
thomas@Toms-MBP Documents % find /Users/thomas/Documents/ -iname "*quotes*" -print
/Users/thomas/Documents/denglish_Quotes.txt
/Users/thomas/Documents/quotes.txt
~~~~

Dieses Verhalten können wir mit dem folgenden Kommando wieder in den Ursprungszustand versetzen:

~~~console
setopt CAse_glob
~~~~

Übrigens, wenn wir `ls` mit `grep` verwenden, funktioniert das ohne vorher ein `unsetopt` ausführen zu müssen.

~~~console
thomas@Toms-MBP Documents % ls -l | grep -i "quote"
-rw-r--r--@  1 thomas  staff   899 Jun 28 11:41 denglish_Quotes.txt
-rw-r--r--@  1 thomas  staff  2306 Jul 24 11:49 quotes.txt
~~~~

Mehr Informationen dazu gibt es auch in der [ZSH Dokumentation](https://zsh.sourceforge.io/Doc/Release/Options).

*[ZSH]: Z Shell - die Standard Shell in mac OS
