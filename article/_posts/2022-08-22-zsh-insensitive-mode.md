---
layout: post
title: ZSH Insensitive Mode
description: >
  ZSH und der ls-Befehl zum Auflisten von Dateien ohne Berücksichtigung der Groß- und Kleinschreibung
sitemap: true
hide_last_modified: true
---

Dateien mit LS auflisten, die einem bestimmer Schema entsprechen machen wir ja eigentlich relativ einfach:

~~~bash
ls -l *quotes:
~~~~

In meinem Fall ist das Ergebnis lediglich eine Datei:

~~~bash
thomas@Toms-MBP Documents % ls -l *quotes*                            
-rw-r--r--@ 1 thomas  staff  2306 Jul 24 11:49 quotes.txt
~~~~

Mac OS und die ZSH sind aber "case sensitive", das heißt hier wird die Groß- bzw. Kleinschreibung berücksichtigt. Dieses Verhalten können wir mit dem folgenden Kommando abschalten:

~~~bash
unsetopt CAse_glob
~~~~

Das Ergebnis danach sieht dann wie folgt aus:

~~~bash
thomas@Toms-MBP Documents % unsetopt CAse_glob
thomas@Toms-MBP Documents % ls -l *quotes*    
-rw-r--r--@ 1 thomas  staff   899 Jun 28 11:41 denglish_Quotes.txt
-rw-r--r--@ 1 thomas  staff  2306 Jul 24 11:49 quotes.txt
~~~~

Natürlich berücksichtigt auch das Kommando `find` diese Einstellung:

~~~bash
thomas@Toms-MBP Documents % find /Users/thomas/Documents/ -iname "*quotes*" -print
/Users/thomas/Documents/denglish_Quotes.txt
/Users/thomas/Documents/quotes.txt
~~~~

Dieses Verhalten können wir mit dem folgenden Kommando wieder in den Ursprungszustannd versetzen:

~~~bash
setopt CAse_glob
~~~~

Übrigens, wenn wir ls mit grep verwenden funktioniert das ohne `unsetopt`.

~~~bash
thomas@Toms-MBP Documents % ls -l | grep -i "quote"
-rw-r--r--@  1 thomas  staff   899 Jun 28 11:41 denglish_Quotes.txt
-rw-r--r--@  1 thomas  staff  2306 Jul 24 11:49 quotes.txt
~~~~

Mehr Informationen dazu gibt es in der [ZSH Dokumentation](https://zsh.sourceforge.io/Doc/Release/Options).

*[ZSH]: Z Shell die Standard Shell in mac OS
