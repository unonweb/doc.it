---
title: Internet Layer
---

Layer 3
==================


* Hardware: Router


NAT
===========

### SNAT (Source-NAT)

Am meisten findet Source-NAT Anwendung aufgrund der Knappheit öffentlicher IPv4-Adressen und der Tendenz, immer mehr Haushalte (in denen private Subnetze verwendet werden) mit dem Internet zu verbinden. Die spezielle Form der SNAT wird auch *Masquerading* bzw. Masquerade genannt, was vor allem bei Einwahlverbindungen genutzt wird. Beim Maskieren wird automatisch durch einen Algorithmus die Absender-Adresse des Pakets in die IP-Adresse des Interfaces geändert, auf dem das Paket den Router verlässt, während beim SNAT die (neue) Quell-Adresse explizit angegeben werden muss.

Gerade in privaten oder möglichst preisgünstig ausgeführten Netzinstallationen wird Source-NAT als eine Art Sicherheitsmerkmal und zur Trennung von internen und externen Netzen eingesetzt. Durch das Maskieren der Quell-IP-Adresse können tatsächlich die internen Rechner nicht mehr von außen direkt angesprochen werden, was aber eher als Nebeneffekt zu betrachten ist, da es weder Sicherheitsinfrastruktur ersetzt noch zur Trennung von Netzen vorgesehen ist.

### DNAT (Destination-NAT)
* wird verwendet, um das Ziel eines IP-Pakets zu ändern. 

Am häufigsten findet DNAT Verwendung beim Ändern der öffentlichen IP eines Internet-Anschlusses in die private IP-Adresse eines Servers im privaten Subnetz. Diese Methode ist als „Port-Forwarding“ in Verbindung mit UDP / TCP - Verbindungen bekannt. DNAT kann daher auch dazu genutzt werden, um mehrere, unterschiedliche Serverdienste, die auf verschiedenen Computern betrieben werden, unter einer einzigen (öffentlichen) IP-Adresse anzubieten. Siehe zur Abgrenzung von DNAT auch NAT-Traversal bzw. NAT-T. 