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

Beim Zugriff auf virtuelle Anwendungen und Desktops hängt das User Experience in hohem Maße von der Netzwerkkommunikation ab, vor allem von der Latenz ab. Unter Latenz versteht man die Zeitspanne zwischen der Anforderung von Daten und dem Erhalt einer Antwort von der angesprochenen Ressource.

Eine hohe Latenz bedeutet im Wesentlichen, dass unserer virtueller Desktop oder die Anwendungen sich auch langsam verhält. Die Benutzer empfinden Verzögerungen bei Tastenanschlägen und Mausbewegungen oder Wiedergabe von Multimediainhalten als besonders störend.  RDP über UDP soll hier die allgemeine Benutzererfahrung erheblich verbessern. Dies gilt insbesondere für Anwendungen, die sehr latenzempfindlich sind wie z.B. grafikintensive Anwendungen. 

## Was ist RDP Shortpath?

Das klassische Microsoft Remote Desktop Prototokoll unterstützt bereits Verbidnungen sowohl über Transmission Control Protocol (TCP) als auch über User Datagram Protocol (UDP). Verbindungen zu Azure Virtual Desktop konnten bisher, architekturbedingt lediglich TCP verwenden. RDP Shortpath ist eine Funktion von Azure Virtual Desktop, die einen direkten UDP-basierten Transport zwischen einem unterstützten Windows Remote Desktop-Client und dem Sitzungshost herstellen kann. 

Standardmäßig versucht dabei das Remote Desktop Protokoll (RDP), eine Verbindung über UDP herzustellen und verwendet einen TCP-basierten Reverse-Connect-Transport als Fallback-Verbindungsmechanismus. Der TCP-basierte Reverse-Connect-Transport bietet die beste Kompatibilität mit verschiedenen Netzwerkkonfigurationen und hat eine hohe Erfolgsquote beim Aufbau von RDP-Verbindungen. Der UDP-basierte Transport bietet eine bessere Verbindungssicherheit sowie eine gleichmäßigere Latenz.

RDP Shortpath kann durch die UDP-Verbindung die Leistung verbessern und die Latenzzeit verringert. Wie bereist erwähnt, ist das besonders vorteilhaft für Benutzer, die mit grafikintensiven Anwendungen arbeiten und erhöht die vom Benutzer wahrgenommene Experience für jede Azure Virtual Desktop-Sitzung deutlich.

## Einfache Konfiguration von RDP Shortpath mit Parallels RAS

RDP Shortpath ist über die Parallels RAS-Konsole relativ einfach und mit wenig Aufwand zu konfigurieren. In der Parallels RAS-Konsole werden lediglich die Eigentschaften des AVD Hostpools unter **Farm > Azure Virtual Desktop > Host Pools**  unter der Registerkarte Host Pools Settings angepasst. Hier wählen wir **RDP Shortpath verwalten** und klicken Sie auf **Konfigurieren** und wählen **RDP Shortpath verwenden** um RDP Shortpath für alle Hosts dieses Azure Virtual Desktop Host Pools zu aktivieren. Über den gleichen Dialog können wir RDP Shortpath bei Bedarf auch wieder deaktivieren.

![Bild 1](https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/AVD-RAS192-1.png)

Standardmäßig verwendet RDP Shortpath einen ziemlich großen Portbereich. Um den Portbereich einzuschränken, aktivieren wir **Use a smaller default range of ports** und geben Sie die Ports an, die Sie verwenden möchten. Der Standardbereich ist 49152-65535. Sollte der gewählte Portbereich dann doch nicht ausreichen, wird als Fallback von Parallels RAS automatisch der größere Bereich aktiviert.

Dieser Portbereich gilt für öffentliche Netzwerke und nur für die ausgehenden Kommunikation. RDP Shortpath für öffentliche Netzwerke benötigt keine eingehenden Ports, da nur ausgehende UDP Konnektivität verwendet wird. In der Parallels RAS-Konsole im Bereich Sitzungen können wir anhand der Metriken **User Experience** überprüfen, ob das UDP-Protokoll für die Benutzerverbindung verwendet wird.

![Bild 2](https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/AVD-RAS192-2.png)

Auf dem gestartete AVD Desktop können wir ebenfalls über die Verbindungseigenschaften überprüfen, ob RDP Shortpath über UDP in der Session aktiv ist.

![Bild 3](https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/AVD-RAS192-3.png)

Alternativ dazu gibt es auch das Tool [Remote Display Analyzer](https://rdanalyzer.com/) welches uns neben dem Verbindungsprotokoll in der Pro Version auch weitere nützlich Informationen zu unserer Verbindung liefern kann.

![Bild 4](https://datablob.oss.eu-west-0.prod-cloud-ocb.orange-business.com/images/AVD-RAS192-4.png)

## RDP Shortpath-Modi

RDP Shortpath für Azure Virtual Desktop kann prinzipiell in zwei verschiedenen Modi verwendet werden, abhängig von der vorhandenen Infrastruktur und den Anforderungen.

**Verwaltete Netze** , bei denen eine direkte Verbindung zwischen dem Client und dem Sitzungshost hergestellt wird, wenn eine private Verbindung, z. B. ein virtuelles privates Netz (VPN), verwendet wird.

**Öffentliche Netze**, bei denen eine direkte Verbindung zwischen dem Client und dem Sitzungshost hergestellt wird, wenn eine Verbindung z.B. über das Internet verwendet wird. Bei der Verwendung einer öffentlichen Verbindung gibt es zwei Verbindungstypen, die hier in der Reihenfolge ihrer Bevorzugung aufgeführt sind:

* Eine **direkte UDP-Verbindung** mit dem STUN-Protokoll (Simple Traversal Underneath NAT) zwischen einem Client und einem Sitzungshost.
* Eine **indirekte UDP-Verbindung** unter Verwendung des TURN-Protokolls (Traversal Using Relay NAT) mit einem Relais zwischen einem Client und einem Sitzungshost. Indirekte Verbindung sind zum Zeitpunkt dieses Beitratgs noch in der Tech Preview.

Der für RDP Shortpath verwendete Transport basiert auf dem Universal Rate Control Protocol (URCP). URCP erweitert UDP um eine aktive Überwachung der Netzbedingungen und sorgt für eine faire und vollständige Nutzung der Verbindungen. URCP arbeitet je nach Bedarf mit niedrigen Verzögerungs- und Verlustwerten.

Mehr Informationen zu [RDP-Shortpath](https://learn.microsoft.com/en-us/azure/virtual-desktop/rdp-shortpath).

Wenn sowohl RDP Shortpath für öffentliche Netze als auch für verwaltete Netze aktiviert ist, kommt ein First-Found-Algorithmus zum Tragen, d. h. es wird die Verbindung verwendet, die zuerst für diese Sitzung hergestellt werden kann.

## Integrierte Verwaltung von RDP Shortpath in Parallels RAS

Wenn Azure Virtual Desktop-Workloads über Parallels RAS bereitgestellt werden, kann RDP Shortpath das Benutzererlebnis verbessern, indem die Latenz durch die Nutzung von UDP-basiertem Transport minimiert wird. RDP Shortpath ist einfach über die Parallels RAS Konsole zu konfigurieren und Metriken über die Nutzung können ebenfalls direkt in der Konsole überprüft werden.
