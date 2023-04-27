---
layout: post
title: RDP Shortpath für Azure Virtual Desktops mit Parallels RAS 19.2
description: >
  Verbindungen zu Azure Virtual Desktops über UDP mit Parallels 19.2 und RDP Shortpath verwenden.
image: 
  path: https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/rdp-shortpath-avd1920.png
sitemap: true
hide_last_modified: true
categories: [article]
tag: [Azure]
comments: false
---

* this unordered seed list will be replaced by the toc
{:toc}

Beim Zugriff auf virtuelle Anwendungen und Desktops hängt das User Experience in hohem Maße von der Netzwerkkommunikation, vor allem von der Latenz ab. Unter Latenz versteht man die Zeitspanne zwischen der Anforderung von Daten und dem Erhalt einer Antwort von der angesprochenen Ressource.

Eine hohe Latenz bedeutet im Wesentlichen, dass unserer virtueller Desktop oder die Anwendungen sich auch langsam verhalten. Die Benutzer empfinden Verzögerungen bei Tastenanschlägen und Mausbewegungen oder Wiedergabe von Multimediainhalten als besonders störend.  RDP über UDP soll hier die allgemeine Benutzererfahrung erheblich verbessern. Dies gilt insbesondere für Anwendungen, die sehr latenzempfindlich sind wie z.B. grafikintensive Anwendungen.

## Was ist RDP Shortpath?

Das klassische Microsoft Remote Desktop Prototokoll unterstützt bereits Verbidnungen sowohl über Transmission Control Protocol (TCP) als auch über User Datagram Protocol (UDP). Verbindungen zu Azure Virtual Desktop konnten bisher, architekturbedingt lediglich TCP verwenden. RDP Shortpath ist eine Funktion von Azure Virtual Desktop, die einen direkten UDP-basierten Transport zwischen einem unterstützten Windows Remote Desktop-Client und dem Sitzungshost herstellen kann.

Standardmäßig versucht dabei das Remote Desktop Protokoll (RDP), eine Verbindung über UDP herzustellen und verwendet einen TCP-basierten Reverse-Connect-Transport als Fallback-Verbindungsmechanismus. Der TCP-basierte Reverse-Connect-Transport bietet die beste Kompatibilität mit verschiedenen Netzwerkkonfigurationen und hat eine hohe Erfolgsquote beim Aufbau von RDP-Verbindungen. Der UDP-basierte Transport bietet eine bessere Verbindungssicherheit sowie eine gleichmäßigere Latenz.

RDP Shortpath kann durch die UDP-Verbindung die Leistung verbessern und die Latenzzeit verringert. Wie bereist erwähnt, ist das besonders vorteilhaft für Benutzer, die mit grafikintensiven Anwendungen arbeiten und erhöht die vom Benutzer wahrgenommene Experience für jede Azure Virtual Desktop Sitzung deutlich.

## Einfache Konfiguration von RDP Shortpath mit Parallels RAS

RDP Shortpath ist über die Parallels RAS-Konsole relativ einfach und mit wenig Aufwand zu konfigurieren. In der Parallels RAS-Konsole werden lediglich die Eigentschaften des AVD Hostpools unter **Farm > Azure Virtual Desktop > Host Pools**  unter der Registerkarte **Host Pools Settings** angepasst. Hier wählen wir **Manage RDP Shortpath**, klicken auf **Configure** und wählen **Use RDP Shortpath** um RDP Shortpath für alle Hosts dieses Azure Virtual Desktop Host Pools zu aktivieren. Über den gleichen Dialog können wir RDP Shortpath bei Bedarf auch wieder deaktivieren.

![Bild 1](https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/AVD-RAS192-1.png)

Standardmäßig verwendet RDP Shortpath einen ziemlich großen Portbereich. Um diesen einzuschränken, aktivieren wir **Use a smaller default range of ports** und geben die entsprechenden Ports an, die verwendet werden sollen. Der Standardbereich ist 49152-65535. Sollte der gewählte Portbereich dann doch nicht ausreichen, wird als Fallback von Parallels RAS automatisch der größere Bereich aktiviert.

Dieser Portbereich gilt nur für öffentliche Netzwerke und auch nur für die ausgehende Kommunikation. RDP Shortpath für öffentliche Netzwerke benötigt keine eingehenden Ports. In der Parallels RAS-Konsole im Bereich Sitzungen können wir anhand der Session-Metriken unter **User Experience** überprüfen, ob das UDP-Protokoll für die Benutzerverbindung verwendet wird.

![Bild 2](https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/AVD-RAS192-2.png)

Auf dem gestartete AVD Desktop können wir ebenfalls über die Verbindungseigenschaften überprüfen, ob RDP Shortpath über UDP in der Session aktiv ist.

![Bild 3](https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/AVD-RAS192-3.png)

Alternativ dazu gibt es auch das Tool [Remote Display Analyzer](https://rdanalyzer.com/) welches uns in der Community Version das vewendete Verbindungsprotokoll anzeigt. In der Pro Version werden noch weitere nützlich Informationen zu unserer Verbindung angezeigt.

![Bild 4](https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/AVD-RAS192-4.png)

## RDP Shortpath-Modi

RDP Shortpath für Azure Virtual Desktop kann prinzipiell in zwei verschiedenen Modi verwendet werden, abhängig von der vorhandenen Infrastruktur und den entsprechenden Anforderungen.

**Verwaltete Netze** , bei denen eine direkte Verbindung zwischen dem Client und dem Sitzungshost hergestellt wird, wenn eine private Verbindung, z. B. ein virtuelles privates Netz (VPN), verwendet wird.

**Öffentliche Netze**, bei denen eine direkte Verbindung zwischen dem Client und dem Sitzungshost hergestellt wird, wenn eine Verbindung z.B. über das Internet verwendet wird. Bei der Verwendung einer öffentlichen Verbindung gibt es zwei Verbindungstypen, die hier in der Reihenfolge ihrer Bevorzugung aufgeführt sind:

* Eine **direkte UDP-Verbindung** mit dem STUN-Protokoll (Simple Traversal Underneath NAT) zwischen einem Client und einem Sitzungshost.
* Eine **indirekte UDP-Verbindung** unter Verwendung des TURN-Protokolls (Traversal Using Relay NAT) mit einem Relais zwischen einem Client und einem Sitzungshost. Indirekte Verbindung sind zum Zeitpunkt dieses Beitratgs noch in der Tech Preview und setzen auch AVD Hosts in einem [Validation Hostpool](https://learn.microsoft.com/en-us/azure/virtual-desktop/create-validation-host-pool#define-your-host-pool-as-a-validation-host-pool) voraus.

Der für RDP Shortpath verwendete Transport basiert auf dem Universal Rate Control Protocol (URCP). URCP erweitert UDP um eine aktive Überwachung der Netzbedingungen und sorgt für eine faire und vollständige Nutzung der Verbindungen. URCP arbeitet je nach Bedarf mit niedrigen Verzögerungs- und Verlustwerten.

Mehr Informationen zu [RDP-Shortpath](https://learn.microsoft.com/en-us/azure/virtual-desktop/rdp-shortpath).

Wenn sowohl RDP Shortpath für öffentliche Netze als auch für verwaltete Netze aktiviert ist, kommt ein First-Found-Algorithmus zum Tragen, d. h. es wird die Verbindung verwendet, die zuerst für diese Sitzung hergestellt werden kann.

## Integrierte Verwaltung von RDP Shortpath in Parallels RAS

Wenn Azure Virtual Desktop-Workloads über Parallels RAS bereitgestellt werden, kann RDP Shortpath die User Experience deutlich verbessern, indem die Latenz durch die Nutzung von UDP-basiertem Transport minimiert wird. RDP Shortpath ist einfach (mit nur 4 Klicks) über die Parallels RAS Konsole zu konfigurieren und entsprechende Metriken über die Nutzung können ebenfalls direkt in der RAS Konsole überprüft werden.

Bei Youtube habe ich ein [kleines Video erstellt](https://youtu.be/ebQA84uPMuU), in dem wir sehen können, wie Parallels RAS bzw. der Parallels RAS Client auf Windows und auf macOS (Linux verhält sich dabei identisch wie macOS) mit Azure Virtual Desktop Host Session umgeht. Interessant dabei ist, dass auf einem Windows System der native [AVD Client](https://learn.microsoft.com/de-de/azure/virtual-desktop/users/remote-desktop-clients-overview) im Hintergrund verwendet wird. Dieser wird bei der Installation des Parallel RAS Client bereits mit installiert. Bei macOS und Linux wird noch der Default Browser mit dem Webclient verwendet, um die Session aufzubauen.

Der Endanwender benötigt lediglich den Parallels RAS Client und kann all seine Ressourcen, unabhängig wo sich diese befinden, aus einer Oberfläche heraus verwenden inkl. eines Azure Virtual Desktop aus der Cloud. Der Administrator hingegen verwaltet alle Ressourcen wie bisher ausschließlich über die RAS Konsole und muss diese auch für die AVD Konfiguration und Benutzerzuweisung nicht verlassen. **Simplicity always wins**.
