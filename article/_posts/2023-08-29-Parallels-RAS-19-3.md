---
layout: post
title: Parallels RAS 19.3 Tech-Preview
description: >
  Das neue Release Parallels RAS 19.3 definiert Image Management neu.
image: 
  path: https://picsur.kngstn.eu/i/3e4ea7af-d147-40ff-870b-8d678c4dcfa0.jpg
sitemap: true
hide_last_modified: true
categories: [article]
tag: [Parallels]
comments: false
---

* this unordered seed list will be replaced by the toc
{:toc}

Ein Versionssprung von 19.2 auf 19.3 hört sich jetzt vielleicht nicht besonders spektakulär an, das Ergebnis kann sich aber trotzdem sehen lassen. Allodu beziehungsweise Parallels zeigt mit der aktuellen Version einmal mehr, dass sie sich hinter den Platzhirschen wie Citrix und VMware nicht verstecken müssen. Tatsächlich wird Parallels RAS immer mehr ein Produkt, welches nicht nur als Alternative zu bereits vorhandenem, sondern vielmehr als komplett eigenständige Lösung zu sehen ist.

Für mich war ein großer Nachteil von Parallels RAS immer der Umgang mit den Master Images und dem Bereitstellen der virtuellen Maschinen. Bis zur Version 19.2 gab es nämlich kein wirkliches Management der Master Images. Wir konnten zwar ein Image in den Wartungsmodus versetzen und die erforderlichen Änderungen wie Updates etc. durchführen, doch danach konnten wir die  Änderungen nur sofort auf die laufenden VDI Maschinen oder Sessions Hosts anwenden, was natürlich durch den dann erforderlichen Reboot einen großen Einfluss auf die Benutzererfahrung gehabt hätte. Natürlich hätten wird das auch manuell zu einem späteren Zeitpunkt machen können, es gab aber eben keine Automatik dafür. Möglichkeiten wie zum Beispiel Citrix diese im Machine Creation Service bietet z.b. die Anwendung der Änderungen erst beim nächsten abmelden bzw. Reboot durchzuführen gab es bisher bei Parallels nicht. Konnte ich diesen Nachteil mit der Planung der Anwendung bei Parallels RAS noch mit Powershell automatisieren, fehlt aber eine Versionierung komplett. Sollten meine zuvor gemachten Änderungen fehlerhaft sein, gab es mit Bordmitteln kein Weg zurück. Natürlich konnte ich das auf Hypervisor Ebene mittels Snapshots auch lösen, aber eben nicht aus dem Produkt heraus.

Diesen kompletten Prozess hat sich Parallels in der Version 19.3 nun angenommen. Was dabei herausgekommen ist kann sich durchaus sehen lassen und entspricht weit mehr als das was wir uns in Projekten gewünscht haben. Die effiziente Verwaltung von Master Images ist ein wesentlicher Aspekt für Unternehmen jeder Größenordnung. Administratoren können einen nahtlosen Betrieb ohne User-Impact nur dann sicherstellen, wenn sie in der Lage sind, mehrere Versionen derselben Vorlage zu erstellen, die als Vorlage für die Bereitstellung von virtuellen Maschinen für Session Hosts, VDIs und Azure Virtual Desktop-Umgebungen verwendet werden.

Die neue Versionierungsfunktion ermöglicht die Validierung neuer Versionen vor der Implementierung in der Produktion, ein einfaches Rollback zu früheren Versionen und die Möglichkeit, verschiedenen Host-Pools für unterschiedliche Benutzer-Personas unterschiedliche Versionen zuzuweisen. In 19.3 wird die Versionierung von Vorlagen auf VMware vSphere, Microsoft Hyper V, Microsoft Azure und Azure Virtual Desktop unterstützt. Andere Anbieter wie zum Beispiel Amazon EC2, Scale Computing und Nutanix AHV werden in den folgenden Updates unterstützt werden. Zusätzlich zur Versionierung werden nun auch Versions-Tags,  als logische Darstellung der genauen Versionen unterstützt. Versions-Tags wie z.B. Produktion, Test sowie benutzerdefinierte Tags können festgelegt werden, um manuelle Konfigurationen zu reduzieren, wenn neue Versionen von einer Gruppe von Endbenutzern verwendet werden sollen, was jetzt das Image Lifecycle Management mit Parallels RAS immens erleichtert.

Versionierte Templates können nun auch verschiedenen Host-Pools zugewiesen werden. Dies erleichtert die Verwendung von Validierungs-Hostpools für Entwicklung oder Tests vor der Einführung in die Produktion und reduziert den Administrationsaufwand durch eine geringere Anzahl von zu verwalteten Images, die für unterschiedliche  Geschäftsbereiche oder Benutzer-Personas verwendet werden sollen. 
Sobald ein Master Image in der Konsole aktualisiert wurde, können Administratoren entscheiden, die Wiederherstellung von virtuellen Maschinen zu einem bestimmten Zeitpunkt zu starten. So können Administratoren die Master Images zum Beispiel während der normalen Arbeitszeiten aktualisieren und gleichzeitig die Wiederherstellung der geklonten virtuellen Maschinen außerhalb der Hauptgeschäftszeiten planen.

![Bild 1](https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/2023-08-29-Parallels-RAS-19-3-02.png)

Administratoren können selbstverständlich auch erzwingen, dass Benutzersitzungen abgemeldet werden, damit die Hosts zum festgelegten Zeitpunkt neu erstellt werden können, oder auch warten, bis sich alle Benutzer abgemeldet haben, bevor die Wiederherstellung stattfindet. In letzterem Fall wird die Benutzersitzung nicht unterbrochen, bis sich der Benutzer abmeldet. Sobald sich der Benutzer erneut anmeldet, kann er auf die vom Administrator bereitgestellte Version des Images zugreifen und dieses starten.

## Powermanagement und Auto-Scaling

Wie bereits von der Verwaltung cloud-basierter Ressourcen bekannt, integriert Parallels RAS das Power-Management sowie Autoscaling bei allen Ressourcen statt wie bisher auf Image-Basis nun in die Hostpool Ebene.  Dies ermöglicht unterschiedliche Konfigurationen pro Host-Pool, die für verschiedene Benutzer-Personas verwendet werden können. Diese Granularität ermöglicht die automatische Skalierung und Energieverwaltung pro Host-Pool statt wie bisher pro Image. Bisher wurden neue virtuelle Maschinen über das Autoscaling zum Hostpool hinzugefügt oder entfernt wenn entsprechende vorher konfigurierte Schwellwerte erreicht wurden. In Projekten kam jedoch oft der Wunsch auf, statt diese Maschinen aus dem Hostpool zu löschen eben diese einfach nur auszuschalten. Dies ist insbesondere mit der neuen Kostenoptimierung in der Version 19.2 für verwaltete Festplatten nützlich. Administratoren können nun zusätzlich zur bereits verfügbaren zeit-basierten Energieverwaltung, Hosts auf der Grundlage der prozentualen Auslastung der Hostpools ein- bwz. ausschalten.

## Weitere Neuigkeiten und Funktionen

Neben dem Image Management wurde intensiv an der Integration von FSLogix gearbeitet. FSlogix ist jetzt bereits eine ganze Weile Teil von Parallels RAS, jedoch wurde lediglich der Profile Disk Teil bisher unterstützt. Mit der Version 19.3 werden nun auch die Office Container vollständig unterstützt und können direkt über die Parallels RAS Management Console konfiguriert werden. Zur Info hier, FSLogix Office Containers (ODFC) ist ein Containertyp, der nur Daten und Einstellungen für Microsoft 365-Produkte wie Outlook, Teams, OneDrive (Personal oder Business) und SharePoint enthält. ODFC-Container können optional mit Profil-Containern in einer dualen Container-Konfiguration verwendet werden, um Microsoft 365-App-Daten in einer anderen VHD als die restlichen Profildaten zu platzieren. Darüber hinaus erweitert Parallels RAS 19.3 auch die FSLogix Konfigurationsoptionen mit der Parallels in der Konsole, einschließlich der Festplattenkomprimierung, erweiterte Protokollierung und Cloud-Cache-Einstellungen.

Neben der Überarbeitung und Modersierung der GUI für die Windows, macOS und dem Webclient wurden natürlich auch noch andere typische modernisierungen vorgenommen. So wird jetzt TLS 1.3 standardmäßig unterstützt. TLS 1.3 bietet mehrere Verbesserungen, darunter ein schnelleres TLS-Handshake und einfachere, sicherere Cipher Suites. Neue Funktionen sorgen für mehr Sicherheit und verbesserte Leistung. Zusätzlich wird jetzt auch FIPS (Federal Information Processing Standard) 140-2 für die Validierung der Effektivität von kryptografischer Hardware unterstützt. Das FIPS-Protokoll gewährleistet einen einheitlichen Standard zum Schutz sensibler Regierungsdaten vor immer ausgefeilteren Cyberangriffen und Bedrohungen. Parallels RAS 19.3 ist FIPS 140-2-konform, da es FIPS 140-2-validierte Module im Parallels Secure Gateway, HALB (High Availability Load Balancers) und Parallels Clients für Windows, macOS und Linux unterstützt.

## Was noch zu erwähnen ist

Mit dieser Version integriert Paralles die Option, alle Agenten automatisch zu aktualisieren, sobald der Primary Connection Broker aktualisiert wurde. Derzeit bekommen wir nur einen Hinweis, das ein Update auch für die anderen Komponenten angewendet werden kann. Damit können manuelle Eingriffe von Administratoren vermieden werden und gleichzeitig dafür sorgen, das die RAS Management Konsole während des Upgrades weiterhin nutzbar ist und nicht durch den Upgrade Prozess blockiert wird.

Neben weiteren Verbesserungen bei den Clients findet sich natürlich die Konfiguration der neuen Features in der Rest API sowie dem PowerShell cmdlets wieder. Für die User Experience wurde noch vieles im Bereich Azure Virtual Desktop, dynamisches Druckermanagement oder Multi-Monitor Betrieb für Linux Clients getan. Nebenbei wurde auch das obligatorische Upgrade für den RAS Gast-Agent aufgehoben um mehr Flexibilität bei Updates zu erhalten.

## Weiterführende Links
* [Download der TechPreview](https://my.parallels.com/ras/beta)
* [What's new](https://www.parallels.com/de/products/ras/whats-new/)