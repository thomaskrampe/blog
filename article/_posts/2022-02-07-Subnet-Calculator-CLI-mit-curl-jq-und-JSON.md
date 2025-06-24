---
layout: post
title: Subnet Calculator
description: >
  Subnet-Calculator im Command Line Interface
image: 
  path: https://picsur.kngstn.eu/i/ddedc8de-c9be-43c0-98f3-8762d626624f.jpg
sitemap: true
hide_last_modified: true
categories: [article]
tag: [macOS]
comments: true
---
## Subnet Calculator

Einfach die IP-Adresse in CIDR Notation per `curl` an die API schicken.

~~~console
curl https://networkcalc.com/api/ip/10.0.1.15/27
~~~

Das Ergebnis ist eine Antwort in JSON:

~~~json
{"status":"OK","meta":{"permalink":"https://networkcalc.com/subnet-calculator/10.0.1.15/27","next_address":"https://networkcalc.com/api/ip/10.0.1.16/27"},"address":{"cidr_notation":"10.0.1.15/27","subnet_bits":27,"subnet_mask":"255.255.255.224","wildcard_mask":"0.0.0.31","network_address":"10.0.1.0","broadcast_address":"10.0.1.31","assignable_hosts":30,"first_assignable_host":"10.0.1.1","last_assignable_host":"10.0.1.30"}}
~~~

Um damit jetzt etwas anfangen zu können, benötigen wir es in einer Datei:

~~~console
curl https://networkcalc.com/api/ip/10.0.1.15/27 > ip.txt
~~~

Jetzt können wir mit [jq][1] die JSON Datei abfragen. Vorher natürlich noch `jq` per Homebrew installieren:

~~~console
brew install jq
~~~

Danach können wir den JSON File parsen. Vorher können wir aber auch erstmal mit einem [Online Parser][2] spielen.

~~~console
cat ip.txt | jq '.address.cidr_notation'
~~~

Um das zu verstehen benötigen wir die JSON Ausgabe "formatiert".

> Übrigens im Visual Studio geht das Formatieren von JSON mit `alt`+ `cmd`+ `m` bzw. `alt` + `m`.

~~~json
{
    "status":"OK",
    "meta":{
        "permalink":"https://networkcalc.com/subnet-calculator/10.0.1.15/27",
        "next_address":"https://networkcalc.com/api/ip/10.0.1.16/27"
        },
    "address":{
        "cidr_notation":"10.0.1.15/27",
        "subnet_bits":27,
        "subnet_mask":"255.255.255.224",
        "wildcard_mask":"0.0.0.31",
        "network_address":"10.0.1.0",
        "broadcast_address":"10.0.1.31",
        "assignable_hosts":30,
        "first_assignable_host":"10.0.1.1",
        "last_assignable_host":"10.0.1.30"
        }
}
~~~

Da JSON wie XML hierarchisch auf gebaut ist gehen wir mit `.address` in den dritten Abschnitt (1. = .status, 2. = .meta, 3. = .address) und holen uns dort das Objekt `.cidr_notation`. Da wir das ja bereits kennen, weil wir das ja beim curl erfasst haben, wäre zum Beispiel die Subnetmaske interessant (die Bit kennen wir ja):

~~~console
curl https://networkcalc.com/api/ip/10.1.101.0/28 > ip.txt 

cat ip.txt | jq '.address.cidr_notation'  
"10.1.101.0/28"
cat ip.txt | jq '.address.subnet_mask'                     
"255.255.255.240"
cat ip.txt | jq '.address.first_assignable_host'
"10.1.101.1"
cat ip.txt | jq '.address.last_assignable_host'
"10.1.101.14"
~~~

Das ganze geht auch mit Powershell etwas komfortabler.

~~~powershell
(Invoke-RestMethod https://networkcalc.com/api/ip/10.0.1.15/27).address
~~~

Ausgabe der API:

~~~powershell
PS /Users/thomas> (Invoke-RestMethod https://networkcalc.com/api/ip/10.0.1.15/27).address

cidr_notation         : 10.0.1.15/27
subnet_bits           : 27
subnet_mask           : 255.255.255.224
wildcard_mask         : 0.0.0.31
network_address       : 10.0.1.0
broadcast_address     : 10.0.1.31
assignable_hosts      : 30
first_assignable_host : 10.0.1.1
last_assignable_host  : 10.0.1.30
~~~

[1]: https://stedolan.github.io/jq/
[2]: https://jqplay.org/#
