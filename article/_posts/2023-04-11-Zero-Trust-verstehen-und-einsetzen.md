---
layout: post
title: Zero-Trust verstehen und einsetzen
description: >
  Was bedeutet Zero-Trust und wie lässt sich dieser Ansatz in bestehende Infrastrukturen integrieren.
image: 
  path: https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/trust-4321822_1920.jpg
sitemap: true
hide_last_modified: true
categories: [article]
tag: [Security]
comments: false
---

**Vertrauen ist gut, Kontrolle ist besser**

Dieses Sprichwort hat wohl jeder bereits irgendwann einmal in seinem Leben gehört und tatsächlich ergibt es nicht nur im alltäglichen Leben, sondern gerade auch in der Informationstechnologie seinen Sinn. Über Zero-Trust wird gerade sehr viel berichtet, aber was ist das und wie kann man diesen Ansatz verwenden?

## Was ist Zero-Trust

Stellen wir uns kurz einmal vor, wir werden auf der Straße von einem Polizisten angesprochen. Dieser trägt eine Uniform und wir können somit relativ schnell erfassen, ob diese „echt“ sein könnte. Wir wissen an dieser Stelle zwar nicht hundertprozentig, ob diese Person auch wirklich ein Polizist ist, bringen ihm aber sicherlich erstmal einen gewissen Vorschuss an Vertrauen entgegen. Wie verhält es sich jedoch, wenn dieser vermeintliche Polizist in Zivil vor uns steht. Wahrscheinlich würden wir ohne eine weitere Verifizierung von Ausweis oder Dienstmarke dem ganzen erstmal sehr kritisch gegenüberstehen und der Person sicherlich nur sehr wenig Vertrauen entgegenbringen. Wie groß wäre unser Vertrauen denn, wenn uns dieser vermeintliche Polizist jetzt nicht persönlich, sondern zum Beispiel über das Telefon anspricht?  Richtig, das Vertrauen wäre gleich null, da auch eine schnelle Verifizierung unmöglich ist.

Wörtlich übersetzt heißt Zero-Trust also „kein Vertrauen“ und genau nach diesem Ansatz müssten wir alle drei Polizisten aus unserem vorherigen Beispiel behandeln und zuerst einmal verifizieren. Zero-Trust in der IT ist auch nicht wirklich etwas ganz Neues, der Begriff taucht erstmals bereits 1994 in einer Doktorarbeit von Stephen Paul Marsh auf. Das Konzept setzt darauf, dass Unternehmen weder ihren Kunden noch ihren Mitarbeitern und auch keinen Anwendungen vertrauen sollten. Unabhängig, ob sich diese innerhalb oder außerhalb der Infrastruktur des Unternehmens befinden. Stattdessen muss Zugriff auf Unternehmensdaten zu jeder Zeit überprüft, kontrolliert und konsequent überwacht und analysiert werden.

Die Cloud Security Alliance (CSA) beschreibt das Konzept als eine Kombination aus den drei Teilen:

* Geräte-Authentifikation
* identitätsbasierter Zugang
* dynamisch bereitgestellte Berechtigungen

### Zero-Trust-Infrastruktur

Integraler Bestandteil einer Zero-Trust Infrastruktur ist ein Konzept, das wir aus vergangenen Jahren bereits kennen. Die Rede ist von AAA bzw. Triple A, oder ausgeschrieben Authentication, Authorization, Accounting – was ich um ein viertes A, nämlich Analytics, ergänzen möchte. Dieses AAAA beachten wir in einer Zero-Trust Infrastruktur nicht nur bei der Anmeldung an einem System, sondern kontinuierlich über die ganze Verbindungsdauer einer Benutzersitzung.

Bisher verhält es sich so, dass unser Benutzer auf ein Anmeldefenster kommt und sich dort mit seinem Benutzername und einem nur ihm bekannten Passwort anmelden konnte. Einmal auf diesem Weg am System angemeldet konnte dieser Benutzer alles machen, was mit seinen ursprünglich definierten Rechten möglich ist. Aus der Erfahrung wissen wir jedoch, dass gerade hier ein ungeheures Angriffspotential ermöglicht wird. Benutzernamen sind oft einfach zu raten, Passwörter sind meist unsicher und selbst sichere Passwörter werden über die unterschiedlichsten Methoden (Social Engineering, Phishing oder Brute Force) schnell komprimiert. Hier findet bereits seit einigen Jahren ein Umdenken statt, was zur vermehrten Einführung von Zwei-Faktor (2FA) bzw. Multi-Faktor Authentifizierung (MFA) geführt hat. In diesem Zusammenhang bedeutet Zwei-Faktor Authentifizierung eine Kombination aus etwas was nur mir bekannt ist z.B. mein Passwort und etwas was ich besitze z.B. einen zusätzlichen Soft- oder Hard-Token. Multi-Faktor setzt darüber hinaus noch weitere Faktoren voraus, so könnte ein interner Benutzer zum Beispiel seinen Benutzernamen mit einem zusätzlichen Benutzerzertifikat meiner PKI verifizieren, ein externer Benutzer muss vielleicht zusätzlich noch einen Code aus einer SMS eingeben. So ist man schnell in der Lage, einen Benutzer und seinen Status zu identifizieren.

Wir wissen jetzt zwar, wer unser Benutzer ist und welche Dienste dieser nutzen darf, was wir immer noch nicht wissen ist, ob sein verwendetes Endgerät vertrauenswürdig ist. Befindet sich unser Benutzer mit seinem Business Notebook gerade in einem Café und ist mit einem unverschlüsselten WLAN verbunden oder schlimmer, verwendet er gerade ein öffentliches Endgerät in einem Internet-Café?

Die reine Identifizierung des Benutzers (auch nicht über mehrere Faktoren) reicht in diesem Fall nicht aus. Wir müssen zusätzlich auch das Endgerät selbst sowie auch den Standort und den Zugangsweg, der für den Zugriff auf unsere Infrastruktur verwendet werden soll, zweifelsfrei identifizieren können. Da sich dieser Kontext ständig ändern kann, wenn sich z.B. der Benutzer mit seinem Endgerät bewegt, müssen wir diese Änderungen auch ständig überwachen und auf Änderungen sofort darauf reagieren. Aber wie sollten wir denn überhaupt darauf reagieren?  Wenn wir den Kontext dieses Benutzers mit seinem Endgerät kennen, können wir entsprechende Zugriffe erlauben oder eben verweigern. Das kann dazu führen, dass ein Benutzer bestimmte Anwendungen oder Daten nicht mehr nutzen darf bis hin zum Beenden der kompletten Kommunikation. Zum Beispiel darf ein leitender Angestellter auf spezielle Business-Anwendungen und deren Daten nur dann zugreifen, wenn er sich geografisch in Deutschland befindet. Sobald er z.B. mit dem Zug die Grenze überfährt, wird basierend auf den GPS-Daten, der Zugriff auf diese Anwendung verweigert. Alle anderen Anwendungen kann er jedoch weiter ohne Einschränkungen verwenden. Oder auch ein Beispiel mit dem derzeit oft genutzten Home-Office. Befindet sich mein Benutzer mit einem Endgerät des Unternehmens in seinem Home-Office, basieren auf einem Gerätezertifikat auf dem Endgerät und der Auswertung seiner WLAN SSID oder der MAC Adresse seines Access Points, darf er eine Verbindung herstellen. Verlässt er das identifizierbare Home-Office wird eine Anmeldung an der Unternehmensinfrastruktur verweigert.

Aber auch die Infrastruktur muss basierend auf Kontext überwacht und analysiert werden. Werden beispielsweise ungewöhnlich viele Daten von den Fileservern übertragen, gibt es plötzlich vermehrt Zugriffe auf bestimmte Daten, meldet sich ein bereits angemeldeter Benutzer gerade erneut aus einem anderen Land an? Aus einer solchen Analyse sollten bzw. müssen aber auch sofortige Aktionen und Maßnahmen erfolgen z.B. sperren des Benutzerkontos bei doppelter Anmeldung, schließen der Firewall Ports, wenn plötzlich Daten in großen Mengen übertragen werden und natürlich das Starten einer weiteren Überwachung solcher Vorfälle zum Beispiel durch Aktivieren von Screen Recording.  Angriffe auf die Datenübertragungsprotokolle können durch konsequente und vor allem durchgängige Verschlüsselung minimiert werden, was gerade in öffentlichen Netzwerken bei der Verwendung von Cloud-Diensten unumgänglich ist.

![][1]

## Am Anfang steht die Planung

Gute Planung und eine Strategie für die Implementierung von Zero-Trust Infrastrukturen ist einer der Erfolgsfaktoren. Solche Systeme können nicht übergreifend eingeführt werden, das wäre erstens viel zu aufwändig und würde wahrscheinlich in einem Verwaltungsalbtraum enden. Die schrittweise Einführung schafft nicht nur Verständnis für das Betriebsteam und den Endanwender, sondern erhöht auch die Sicherheit schrittweise immer weiter. Wichtig zu beachten ist hier ein benutzerzentrierter Ansatz, denn anders als der Name vielleicht vermuten lässt schafft Zero-Trust bei richtiger Implementierung die Balance zwischen Sicherheit und problemloser Nutzung und erhöht damit nicht nur die Sicherheit. sondern auch die User Experience. Bei allen Aktivitäten in diesem Bereich sollten die Benutzer immer mit einbezogen werden. Nichts ist schlimmer als fehlende Akzeptanz bei den Benutzern für eine solche Lösung.

### Grundprinzipien von Zero-Trust

Das Zero Trust Security-Konzept basiert auf den folgenden Prinzipien, mit denen Zugriffe auf oder innerhalb eines Unternehmens gesichert werden können.

**1\. Zugriff mit geringsten Berechtigungen:**

Dies ist ein grundlegendes Konzept, bei dem Benutzern nur die Zugriffsebene gewährt werden, die sie benötigen, um ihre Aufgabe zu erfüllen. Es reduziert die Fläche, die für einen eventuellen Angriff genutzt werden kann.

**2\. Identifikation von Benutzern, Endgeräten und Standorten:**

Sie müssen wissen, Wer, Was, Wann, Womit und von Wo Zugriff auf Ihr Netzwerk, Ihre Anwendungen, Ihre Daten etc. gewährt wurde. Jede Zugriffsanforderung muss immer den AAA(A) (Authentifizierung, Autorisierung und Accounting) Prozess durchlaufen.

**3\. Mikrosegmentierung:**

Sicherheitsbereiche werden in kleinere Zonen aufgeteilt und dienen dazu sicherzustellen, dass für verschiedene Teile Ihres Netzwerks eine separate Überwachung bereitgestellt werden kann. Das macht die Überwachung einfacher, da weniger Daten pro Segment verarbeitet werden müssen. Zusätzlich bietet diese Segmentierung eine detaillierte Zugriffssteuerung, um übermäßige Berechtigungen zu vermeiden.

**4\. Nutzung fortschrittlicher Präventionstechniken:**

Es sollten vorbeugende Präventionstechniken angewendet werden, mit denen Online-Verstöße gestoppt und Schäden reduziert beziehungsweise ausgeschlossen werden können. Insbesondere Multi-Faktor Authentifizierung (MFA) oder auch eine durchgehende Verschlüsselung von Daten im Storage als auch während des Transports sind solche Techniken.
Analytics und Monitoring spielt eine zentrale Rolle

Heutige Analyse- und Monitoringtools sind in Kombination mit KI und Machine Learning Technologien mächtige Werkzeuge, um ein untypisches Verhalten sofort zu erkennen. Dabei können diese Anomalien auch sehr komplex sein, etwa ein untypisches Arbeitsmuster eines Mitarbeiters, das diverse beteiligte Anwendungen sowie sensible Daten enthält und sich vielleicht über mehrere Tage erstreckt. Auf das Erkennen eines solchen Verhaltens muss das System unverzüglich automatisch reagieren, beispielsweise indem der Zugriff auf bestimmte Daten entzogen wird. Zero-Trust funktioniert nicht ohne Analytics und Monitoring sowie ein entsprechendes Regelwerk.

### Was muss geschützt werden

Was es zu schützen gibt, wird definiert durch die Informationen, die es abzusichern gilt. Zu beachten ist auch, dass Zero-Trust-Schutz über die reinen Daten hinausgeht und auch andere Elemente betrifft. Bei der Definition geschützt werden muss, sollten alle kritischen Daten, Anwendungen, Assets und auch Services berücksichtigt werden. Dabei müssen nicht nur die Standardbenutzer, sondern auch Benutzer mit normalerweise erhöhten Rechten berücksichtigt werden. Gerade die Administratoren sollten ausschließlich mit verwalteten Rechten ausgestattet werden, die lediglich für eine definierte Aufgabe und einen definierten Zeitraum gültig sind. Servicekonten oder auch lokale Administrationskonten sollten mit Tools wie LAPS (Local Administrator Password Solution) oder gMSAs (Group Managed Service Accounts) verwaltet werden.

### Entwerfen von Richtlinien

Das Entwerfen und Anwenden von Richtlinien ist ein lebender Prozess und muss ständig gegen die Information aus den Analyse- und Überwachungsergebnissen überprüft werden. Frei nach Aristoteles helfen uns die 5 W’s bei der Informationsbeschaffung und der Problemlösung.

    For in acts we must take note of who did it, by what aids or instruments he did it (with), what he did, where he did it, why he did it, how and when he did it. 

Ins Deutsche übersetzt und etwas vereinfacht heißt das so viel wie **Wer, Was, Wann, Wo, und Warum**. 

Und genau diese Fragen müssen wir in unserer Zero-Trust Infrastruktur für jeden stattfindenden Transfer von Daten beantworten und entsprechende Regel definieren, was geschehen soll wenn eine der Antworten nicht mehr passt. Die Überwachung und Protokollierung des gesamten Datenverkehrs im Netzwerk ist eine zentrale Komponente von Zero Trust. Es ist wichtig, dass das System so viel Telemetrie Daten wie möglich erfasst. Diese Daten liefern uns ständig neue Erkenntnisse darüber, wie sich das Zero-Trust-Netzwerk im Laufe der Zeit verbessern lässt, indem wir die Beantwortung der **fünf W Fragen** kontinuierlich wiederholen.

## Fazit

Die Einführung einer Zero-Trust Infrastruktur ist sicherlich kein leichtes Projekt. Unternehmen allerdings, die bereits die Cloud adaptiert haben, kommen in den Genuss von leicht zu implementierenden, Cloud-basierten Zero-Trust Sicherheitsmodellen, die fast alle großen Cloudprovider und Hyperscaler anbieten. Aber auch im eigenen Rechenzentrum sind bereits entsprechende Tool Sets vorhanden, die die Einführung einer Zero-Trust Infrastruktur vereinfachen. Abschließend noch ein weiteres Sprichwort – Jede Reise beginnt mit dem ersten Schritt. Es gibt keinen Grund den ersten Schritt in Richtung Zero-Trust nicht zu machen.

[1]: /assets/img/article/Zero-Trust-Infrastruktur-1024x407.png