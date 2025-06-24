---
layout: post
title: macOS Speedtest mit networkQuality
description: >
  Wer die Qualität seiner Internetverbindung überprüfen möchte kann jetzt auf Build-in Tools zurückgreifen.
image: 
  path: https://picsur.kngstn.eu/i/081873fe-b009-4c32-84c5-e80166549f82.jpg
sitemap: true
hide_last_modified: true
categories: [article]
tag: [macOS]
comments: false
---

Übrigens wer mal einen Speedtest machen möchte, macOS Monterey hat den Befehl `networkQuality` im Terminal dabei. Das Kommando prüft dabei Up- und Downstream gleichzeitig, was ein deutlich realistischeres Bild liefert, als beispielsweise ein Test bei speedtest.net. 

Mit dem Schalter `–s` führt networkQuality den Test aber auch sequentiell aus.

~~~console
thomas@Toms-MBP ~ % networkquality

==== SUMMARY ==== 
Upload capacity: 9.053 Mbps
Download capacity: 44.213 Mbps
Upload flows: 16
Download flows: 20
Responsiveness: Medium (390 RPM)
~~~~

Gerade für z.B. Audio / Video Konferenzen ist der Wert Responsiveness interessant, hier wird mit Low, Medium oder High die Netzwerkqualität bewertet. Gemessen wird hier in „Roundtrips Per Minute (RPM)“, die laut Apple die Anzahl der aufeinanderfolgenden Roundtrips oder Transaktionen angeben, die ein Netzwerk unter normalen Bedingungen in einer Minute durchführen kann.

Ein wenig mehr Informationen liefert ein Verbose mit dem Schalter `-v`:

~~~console
thomas@Toms-MBP ~ % networkquality -v 

==== SUMMARY ====
Upload capacity: 8.179 Mbps 
Download capacity: 42.537 Mbps 
Upload flows: 16 
Download flows: 12 
Responsiveness: High (2727 RPM) 
Base RTT: 18 
Start: 18.11.21, 10:18:14 
End: 18.11.21, 10:18:26 OS 
Version: Version 12.0.1 (Build 21A559)
~~~~

Wer das Ergebnis weiterverarbeiten möchte, kann auch mit dem Schalter `-c` (kleines c) eine JSON Ausgabe erzeugen. Mit dem Schalter `-C` (also ein großes C) kann die Config URI geändert werden. Wer mehr Wissen möchte, verwendet den Schalter `-h` für die Hilfe.

~~~console
thomas@Toms-MBP ~ % networkquality -h

USAGE: networkQuality [-C <configuration_url>] [-c] [-h] [-I <interfaceName>] [-s] [-v]
    -C: override Configuration URL
    -c: Produce computer-readable output
    -h: Show help (this message)
    -I: Bind test to interface (e.g., en0, pdp_ip0,...)
    -s: Run tests sequentially instead of parallel upload/download
    -v: Verbose output
~~~~

Abschließend noch ein paar weitere Details. Auf meinen Mac möchte das Terminal dafür eine Verbindung über HTTPS zu defra3-edge-bx-013.aaplimg.com aufbauen, für alle die den ausgehenden Traffic z.B. mit [Little Snitch](https://www.obdev.at/products/littlesnitch/index.html) im Auge behalten wollen.

Für die Konfiguration wird `https://mensura.cdn-apple.com/api/v1/gm/config` verwendet. Der dort angegebene Endpoint wird von der API dynamisch, basierend vom Standort der jeweiligen Messung generiert (z.B. defra = Deutschland/Frankfurt, nlams = Niederlande/Amsterdam usw.). Wer also seine ausgehende Firewall konfigurieren möchte, sollte mit Allow 443 out für *.aaplimg.com und mensura.cdn-apple.com auf dem richtigen Weg sein.

~~~json
{ 
   "version": 1, 
   "urls": { 
      "small_https_download_url": "https://mensura.cdn-apple.com/api/v1/gm/small", 
      "large_https_download_url": "https://mensura.cdn-apple.com/api/v1/gm/large", 
      "https_upload_url": "https://mensura.cdn-apple.com/api/v1/gm/slurp" 
   }, 
   "test_endpoint": "nlams2-edge-bx-032.aaplimg.com" 
}
~~~~
