---
layout: post
title: Microsoft CA Maintenance 
description: >
  Auch eine Microsoft Zertifizierungsstelle braucht Aufmerksamkeit und Pflege.
image: 
  path: https://picsur.kngstn.eu/i/1f33355c-df10-4281-af4e-adf02eac8fc0.jpg
sitemap: true
hide_last_modified: true
categories: [article]
tag: [Microsoft]
comments: false
---

* this unordered seed list will be replaced by the toc
{:toc}

Einmal installiert stellen die Microsoft Active Directory Zertifikatdienste (AD CS), also die Zertifizierungsstelle (CA) von Microsoft, entsprechend benötigte Zertifikate aus.

Je nach dem welche Zertifikat Templates installiert und bereitgestellt werden sollen, können dies eine ganze Menge an Zertifikaten werden. Ganz besonders dann, wenn zum Beispiel Lösungen wie die Federated Authentication Services (FAS) von Citrix oder ähnliche Produkte eingesetzt werden. Obwohl meistens alles reibungslos funktunioniert, braucht auch so eine CA ab und zu ein wenig Pflege.

## Zertifikate

Zunächst müssen wir verstehen, was alles genau in der CA-Datenbank gespeichert wird. Wenn ein neuer Zertifikatsantrag bei der Zertifizierungsstelle eingereicht wird, wird eine neue Zeile in der Datenbank erstellt. Während die Anfrage von der Zertifizierungsstelle bearbeitet wird, werden die verschiedenen Felder in dieser Zeile aktualisiert und der Status jeder Anfrage zu einem bestimmten Zeitpunkt beschreibt, an welchem Punkt des Prozesses sich die Anfrage befindet.

Möglichen Zustände für jede Datenbankzeile sind:

 * **Pending (Ausstehend)** Ein ausstehender Antrag wird so lange zurückgestellt, bis ein Administrator den Antrag manuell genehmigt hat. Nach der Genehmigung wird der Antrag erneut zur Bearbeitung an die Zertifizierungsstelle weitergeleitet. Bei einer Standalone-CA werden alle Zertifikatsanträge standardmäßig zurückgestellt. Bei einer Unternehmens-CA werden Zertifikatsanforderungen zurückgestellt, wenn in der Zertifikatsvorlage die Option "Genehmigung durch den CA-Manager erforderlich" ausgewählt wurde.

* **Failed (Fehlgeschlagen)** Eine fehlgeschlagene Anforderung ist eine Anforderung, die von der Zertifizierungsstelle abgelehnt wurde, weil sie nicht für die Richtlinien der Zertifizierungsstelle geeignet ist oder weil bei der Erstellung des Zertifikats ein Fehler aufgetreten ist. Ein Beispiel für einen solchen Fehler ist, wenn die Zertifikatsvorlage so konfiguriert ist, dass eine Schlüsselarchivierung erforderlich ist, in der CA aber keine Schlüsselwiederherstellungsagenten konfiguriert sind.

* **Issued (Ausgestellt)** Die Anforderung wurde erfolgreich verarbeitet und das Zertifikat wurde ausgestellt.

* **Revoked (Widerrufen)** Die Zertifikatsanforderung wurde bearbeitet und das Zertifikat ausgestellt, aber der Administrator hat das Zertifikat widerrufen.

Diese Zustände und die Tatsache, ob ein Zertifikat abgelaufen ist oder nicht, müssen bei der Entscheidung welche Zeilen gelöscht werden sollen berücksichtigt werden.

Zeitlich gültige und ausgestellte Zertifikate (Issued) oder auch zeitlich gültige aber widerrufene Zertifikate (Revoked) können **_nicht_** mit dem CA Manager gelöscht werden. Diese Informationen sind für die regelmäßige Erstellung der Zertifikatswiderrufsliste (CRL) erforderlich.

Wenn ein Zertifikat jedoch abgelaufen ist, können wir es mit den Zertifikatsdiensten löschen. Wenn jedoch die Schlüsselarchivierung aktiviert ist, sind möglicherweise auch private Schlüssel in der Datenbankzeile gespeichert. Sobald wir die Zeile löschen würden, können Sie diese privaten Schlüssel nicht mehr wiederhergestellt werden.

Bleiben jetzt noch fehlgeschlagene und ausstehende Anfragen übrig. Bei diesen Zeilen handelt es sich lediglich um Anfragen, es sind keine ausgestellten Zertifikate mit ihnen verbunden.

Ein Administrator kann zwar eine fehlgeschlagene Anforderung technisch gesehen erneut an die Zertifizierungsstelle übermitteln, doch hat dies wenig Sinn, solange die Ursache des ursprünglichen Fehlschlags nicht behoben ist.

In der Praxis können wir fehlgeschlagene Anfragen sicher löschen. Ausstehende Anfragen sollten von einem Administrator geprüft werden, bevor sie gelöscht werden. Ein ausstehender Antrag bedeutet, dass jemand da draußen ein Zertifikat angefordert hat, auf das er geduldig wartet. Der Administrator sollte alle ausstehenden Anträge durchgehen und entweder ausstellen oder ablehnen um die Warteschlange zu leeren, anstatt die Datensätze einfach zu löschen.

### Zuerst mal das Problem beseitigen

Bevor wir allerdings damit beginnen, die fehlgeschlagenen Anfragen aus der Datenbank zu löschen, sollten wir sicherstellen, dass Sie alle Konfigurationsprobleme behoben haben, die zu diesen Fehlern geführt haben. Wenn wir jetzt die fehlgeschlagenen Anfragen löschen, werden sich diese ohne Beseitigung des eigentlichen Problems, schnell wieder herstellen.

Wenn die grundlegenden Probleme, die zu den fehlgeschlagenen Anforderungen geführt haben, behoben sind, überwachen wir das Ereignisprotokoll, um sicherzustellen, dass die Zertifikatsdienste keine weiteren fehlgeschlagenen Anforderungen protokollieren. In einer sehr großen Umgebung sind einige fehlgeschlagene Anfragen allerdings auch zu erwarten.

Sobald keine fehlgeschlagenen Anforderungen mehr protokolliert werden, können wir mit dem Löschen von Zeilen aus der Datenbank beginnen.

### Löschen der fehlgeschlagenen Anfragen

Der nächste Schritt in diesem Prozess ist das tatsächliche Löschen der Zeilen mit dem bewährten Kommandozeilenprogramm `certutil.exe`. Der Parameter `-deleterow` (ab Windows Server 2003) kann zum Löschen von Zeilen aus der CA-Datenbank verwendet werden.

Geben Sie einfach den Typ der zu löschenden Datensätze und ein Datum an, das natürlich in der Vergangenheit liegen sollte (wenn Sie ein Datum angeben, das dem aktuellen Datum oder einem späteren Datum entspricht, schlägt der Befehl fehl). Certutil.exe löscht dann die Zeilen des angegebenen Typs, bei denen das Datum, an dem die Anfrage bei der CA eingereicht wurde (oder das Ablaufdatum bei ausgestellten Zertifikaten), vor dem angegebenen Datum liegt.

Die wichtigsten Datensatztypen sind:

| Name      | Beschreibung        | Datentyp           |
|:----------|:--------------------|:-------------------|
| Request   | Failed und Pending  | Ausstellungsdatum  |
| Cert      | Expired und Revoked | Ablaufdatum        |
| CRL       | CRL Tabelle         | Ablaufdatum        |

Es existieren noch weitere, die sind aber eher uninteressant für diesen Artikel.

Wenn wir jetzt zum Beispiel alle fehlgeschlagenen Anfragen vom 10.1.2023 (und ältere) löschen möchten, verwenden wir das folgenden Kommando in einer administrativen CMD oder PowerShell Session auf dem Server, auf dem auch die Zertifizierungsdienste installiert sind:

~~~console
certutil -deleterow 1/10/2023 Request
~~~

In einigen Fällen bricht das Tool bei mehr als 3000 zu löschenden Zeilen in der Datenbank ab. Hier hilft ein kleiner Trick mit einem Batch Skript und einer Schleife.

~~~batchfile
REM file: "DeleteFailedCerts.cmd"
 
@echo off

:Top
certutil -deleterow 1/10/2023 Request
If %ERRORLEVEL% EQU -939523027 goto Top
~~~~

Natürlich können wir auch bereits abgelaufene und zurückgezogenen Zertifikate mit dem folgenden Kommando löschen:

~~~console
certutil -deleterow 1/10/2023 Cert
~~~~

Letzteres ist vielleicht nicht ganz gewollt, da hier eben alle Zertifikate (**und die dazugehörigen Schlüssel**) aus der Datenbank gelöscht werden. Gerade für diesen Einsatzzweck habe ich ein Powershell Skript geschrieben, dass nicht nur basierend auf dem Datum die Zertifikate löscht, sondern auch das Template, mit welchen die Zertifikate erstellt wurden berücksichtigt.

Das Skript ist in meinem [GitHub Repository][1] erhältlich. Der ursprünglche Einsatzzweck ist die Zertifikate zu löschen, die von Citrix FAS angelegt wurden. Das Skript kann aber leicht angepasst werden.

## Datenbank komprimieren
Der nächste Schritt ist die Komprimierung der CA-Datenbankdatei, um den gesamten Leerraum zu defragmentieren, der durch das Löschen der fehlgeschlagenen Anfragen in der Datenbank entstanden ist. Dieser Vorgang ist identisch mit dem Defragmentieren von Active Directory, da die Zertifizierungsdienste die gleiche ESE Datenbanktechnologie wie das Active Directory verwendet.

Zuerst löschen wir noch alle edb000xx.log Dateien sowie die edb.chk Datei. Wenn die CA mit den Default Einstellungen installiert wurde, sollten sich diese Dateien unter `%SystemRoot%\System32\CertLog` befinden. Vor dem Löschen bitte die CA Services stoppen, da die Dateien sonst noch in Benutzung sind.

![][2]

Da die Zertifizierungsstelle auch während des Defragmentierens nicht online sein darf, lassen wir die Dienste noch deaktiviert und führen danach den folgenden Befehl zum defragmentieren aus:

~~~console
esentutl /d Pfad\zur\CaDatabase.edb
~~~~

![][3]

Im Hintergrund erstellt `esentutl.exe` eine temporäre Datenbankdatei und kopiert alle aktiven Datensätze aus der aktuellen Datenbankdatei in die neue Datei. Wenn der Vorgang abgeschlossen ist, wird die ursprüngliche Datenbankdatei gelöscht und die temporäre Datei so umbenannt, dass sie der ursprünglichen Datei entspricht. Der einzige Unterschied ist jetzt, dass die Datenbankdatei nun viel kleiner sein sollte.

Abschließend natürlich nicht vergessen, die Zertifizierungsstelle wieder zu starten.

## Fazit

Wie jeder andere Infrastrukturdienst in einer Windows Umgebung erfordert auch die Windows-Zertifizierungsstelle eine gewisse Aufmerksamkeit, Wartung, Pflege und Überwachung, um ihre Funktionsfähigkeit auf Dauer gewährleisten zu können. Mit der richtigen Überwachung können wir ernste Probleme erkennen  bevor sie auftreten und mit regelmäßiger Wartung sowie Pflege können wir verhindern, dass ernsthafte Probleme überhaupt auftreten.

*[ESE]: Extensible Storage Engine
*[CRL]: Certificate Revocation List
*[FAS]: Federated Authentication Service

[1]: https://github.com/thomaskrampe/PowerShell/tree/master/Citrix/FAS
[2]: /assets/img/article/microsoft-ca-maintenance-01.png
[3]: /assets/img/article/microsoft-ca-maintenance-02.png
