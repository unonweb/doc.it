---
title: Transport Layer
---

Layer 4
===============

Ports
========

* Schnittstellen zwischen Transportprotokollen und Anwendungsprotokollen
* Endpunkte der TCP/UDP-Kommunikation (definierte Übergabevereinbarung)
* Es gibt verschiedene Ports für TCP und UDP, wobei häufig diesselben Nr. verwendet werden 

#### Well know ports

Ports 0-1023


TCP
===========

* Stellt vor und während der Kommunikation sicher, dass die Gegenstelle empfängt
* langsam

### TCP-Handshake

#### Host A
1) SYN: Verbindungsaufbau erwünscht
#### Host B
2) SYN ACK: Empfangsbereit
#### Host A
3) ACK: Verbindung aufgebaut; Daten können übertragen werden


UDP
==========

* verbindungslos
* keine Session
* unzuverlässig
* schnell
* Verwendung: Broadcasts 