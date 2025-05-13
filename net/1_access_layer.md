---
title: Network Access Layer
---

# Hardware

## Kabel & Leitungen

### Teilnehmeranschlussleitung (TAL)

englisch local loop, auch Amtsleitung oder umgangssprachlich letzte Meile genannt

stellt innerhalb eines Telefonnetzes die Verbindung zwischen der Ortsvermittlungsstelle des Netzbetreibers und dem Telefonanschluss des Dienstnutzers (Teilnehmer, Subscriber) innerhalb des Hauses dar.

Die TAL ist ein Kabel, das typischerweise aus einer Kupfer-Doppelader (CuDa) pro Teilnehmer (d. h. zwei elektrischen Leitungen) besteht.

Anstatt über die individuelle Teilnehmeranschlussleitung des Telefonnetzes können Telefonie und Internetzugänge auch über Ethernet, Kabelmodems, Funk (Wireless Local Loop, Wimax), Mobilfunk oder die Stromleitung (PowerLAN) realisiert werden.

Im Zuge der Migration des herkömmlichen leitungsvermittelten Festnetzes zu einem IP-basierten Next Generation Network werden auf längere Sicht sämtliche Anschlussleitungen im Zugangsnetz DSL-fähig ausgebaut. 

### Lan vs. Ethernet

Das LAN-Kabel ist lediglich ein Oberbegriff. Darunter fallen alle Kabel, die dafür geeignet sind, ein LAN aufzubauen. Neben dem Standard Ethernet existierten auch noch die Standards Arcnet und Token Ring.

Das Ethernet hat sich durchgesetzt und ist mittlerweile der gängige Standard. Es handelt sich dabei um ein in sich gedrehtes Kupferkabel.

### CAT 5/6/7...

#### CAT 5: Der Standard

- CAT-5-Kabel sind in den allermeisten Installationen zu finden und werden daher als Standard betrachtet

- CAT-5-Kabel erreichen Signalraten von bis zu 100 MHz und sind damit für den Gigabit-Betrieb geeignet
- Sie werden auch in professionellem Umfeld noch verlegt, wenn auch nicht mehr so häufig wie CAT 6 oder CAT 7
- Für Heimanwender sind CAT-5-Kabel in den allermeisten Situationen völlig ausreichend.

#### CAT 6: Der Alleskönner

professioneller Bereich 

- CAT-6-Kabel erreichen Betriebsfrequenzen von bis zu 250 MHz. Ihre Übertragungsgeschwindigkeit nimmt aber ab, je länger das Kabel ist
- Die Kabel der CAT-6-Kategorie werden in der gesamten Netzwerkinfrastruktur des öffentlichen Lebens eingesetzt
- Der CAT-6a-Standard erreicht sogar Betriebsfrequenzen von bis zu 500 MHz und ist somit für 10-Gigabit-Ethernet geeignet
- Um dem Standard gerecht zu werden, muss das Kabel besonders vor äußeren Einflüssen wie Nebensignalen oder Rauschen geschützt sein.

#### CAT 7: High Speed

Der CAT-7-Standard ist der schnellste und am besten abgeschirmteste Standard unter den Netzwerkkabeln

- CAT-7-Kabel erreichen eine Betriebsfrequenz von bis zu 600 MHz. Die Unterkategorie CAT-7a schafft sogar bis zu 1.000 MHz.
- Alle Kabel der CAT-7-Kategorie haben 4 separat abgeschirmte Aderpaare in einer gesamten Abschirmung. Dadurch kommen so gut wie keine Nebensignale an das Kabel heran.
- Für Netzwerkkabel der CAT-7-Klasse gibt es genau 2 unterschiedliche genormte Steckertypen.

## Stecker

#### RJ-45 / Lan / ISDN / Ethernet

Im Netzwerkbereich wird oft jeder vollbestückte achtpolige (8P8C) Modularstecker „RJ-45“ genannt. Die davon abgeleitete Bezeichnung GG-45 stellt wie TERA eine Variante für die Anwendung in Datenkommunikationssystemen der Kategorie 7 dar. In Deutschland werden ungeschirmte vollbestückte achtpolige (8P8C) Modularstecker umgangssprachlich auch als ISDN-Stecker, geschirmte als „Ethernet-Stecker“ bezeichnet. 

## modem 

- aus Modulator und Demodulator gebildetes Portmanteauwort,
- ein Kommunikationsgerät, um digitale Signale über weite Übertragungswege zwischen zwei digitalen Endgeräten auszutauschen.
- Vom sendenden Modem wird ein digitales Signal auf eine Trägerfrequenz im Hochfrequenzbereich aufmoduliert, vom empfangenden Modem wird daraus die ursprüngliche Information durch Demodulieren zurückgewonnen.

## splitter

- Elektrotechnisch gesehen ist ein Splitter eine Frequenzweiche.
- ein Gerät, das die Frequenzen von Telefon und DSL, die gemeinsam über eine Teilnehmeranschlussleitung (TAL) übertragen werden, in Empfangsrichtung aufteilt und in Senderichtung zusammenführt.  

- wird an beiden Enden der Teilnehmeranschlussleitung benötigt.
- All-IP-Anschlüsse (auch Next Generation Network) können splitterlos ausgeführt werden und dann die niedrigen Frequenzbereiche für zusätzliche Übertragungskapazität nutzen.  

#### Anschlüsse

An einem in Deutschland üblichen Splitter befinden sich folgende drei Buchsen:

- Eine RJ-Buchse für den Anschluss an die TAE-Buchse mit Hilfe des mitgelieferten Kabels (wie beim NTBA), bei der die Kontakte 1 und 6 mit Amt a/b belegt sind. Falls das mitgelieferte TAE-Kabel zu kurz ist, kann stattdessen ein längeres Installationskabel verwendet und direkt an den Klemmen Amt a/b des Splitters angeklemmt werden. Es sollte dann ein Kabel mit verdrillten Drähten verwendet werden, längere Flachkabel sind zu vermeiden.
- Eine NFN-TAE-Buchse zum Anschluss von analogen Telefonen, Faxgeräten, Anrufbeantwortern und Modems.
- Eine „RJ-45“-Buchse (Modular-Buchse 8P2C), bei der die Kontakte 4 und 5 mit DSL a/b belegt sind.

Layer 1
=============

### Token Ring

* Keine Kollisionsgefahr


Ethernet
------------

* Bustopologie: 
* Kollisionsgefahr (Kollisionen kommen bei Ethernet oft vor. Dabei werden die Datenpakete zerstört)
* Kann sehr langsam werden, wenn die Anzahl der Kollisionen steigt.
* Keine festgelegt Richtung in der die Daten gesendet werden

### Topologie

#### Bus

![ethernet_bus](img/ethernet_bus.png)

#### Stern
* verwendet einen *Hub*
* funktioniert ebenfalls als Bus (der Bus befindet sich im Hub)

![ethernet_star](img/ethernet_star.png)


Busse
-------

* Leitung/Kabel, an der mehrere Verbraucher angeschlossen sein können.
* Beispiel: ISDN-Bus
* Terminatoren/Abschlusswiderstände (50 Ohm)


Hubs
-----------

* Kommen in Sterntopologien zum Einsatz
* Hub = Verdrahtungseinheit & Signalverstärker: Da Signale schwächer werden wenn das Kabel unterbrochen und wieder neu zusammengesetzt wird (wie es bei Steckern der Fall ist), sind Hubs heute i.d.R. aktiv, d.h. mit Stromversorgung

#### Nachteile

Ein Hub schickt ein Signal, das er auf einem Port erhält auf allen anderen Ports wieder raus, da er auf Schicht 1 keine Möglichkeit hat das Ziel auszulesen.  

![hub_disdavantage](img/hub_disadvantage.png)


Layer 2
===========

## Netzwerkkarte

Schnittstelle zwischen Computer und Kabel

* Empfangen der Daten vom Computer & Umwandeln in elektrische Signale
* Empfangen der elektrischen Signale & Umwandeln in Daten
* Prüfung des Adressaten (*MAC*)
* Steuerung des Datenflusses durch das Kabel (*Ethernet* oder *Token Ring*)

### Autonegotiation
oder Auto-sensing bezeichnet ein Verfahren, das es zwei miteinander verbundenen Ethernet-Netzwerkports (z.B. den Netzwerkports eines Computers und denen des Routers, Hubs oder Switches, mit dem dieser z. B. verbunden ist) erlaubt, selbständig die maximal mögliche Übertragungsgeschwindigkeit und das Duplex-Verfahren miteinander auszuhandeln und zu konfigurieren. Das Verfahren gilt nur für Mehrdrahtverbindungen (Twisted-Pair-Kabel) – nicht aber für WLAN-, Glasfaser- oder Koaxialkabelverbindungen. 


Kollisionsvermeidung
---------------------

... beim Zugriff mehrerer Netzwerkstationen auf denselben Übertragungskanal

### CSMA

* **Carrier Sense** (Trägerprüfung): Überprüfung vor dem Senden, ob das Medium wirklich frei ist.
* **Multiple Access**:
  
### /CD

Netzwerkkarten erkennen Kollisionen.  
Sie arbeiten nach dem Prinzip CSMA/CD (**Collision Detection**). Dazu benutzen sie eines der zwei Drähte des Kabels und *horchen* ins Netzwerk. Wenn sie eine Kollision bemerken, senden sie ihre Daten erneut. Sie warten jedoch eine zufällige Zeit ab, damit nicht die Gegenstelle ihre Daten zur gleichen Zeit sendet.

### /CA

**Collision Avoidance**
* WLAN

#### Ablauf
1) *Carrier Sense*: Zuerst wird das Medium abgehört („horcht“).
2) Ist das Medium für die Dauer eines DIFS frei, wird eine Backoffzeit aus dem Contention Window ausgewürfelt und nach Ablauf dieser gesendet.
3) Ist das Medium belegt, wird der Backoff bis zum Ablauf des Network Allocation Vectors (NAV) gestoppt, bevor er nach einem weiteren DIFS entsprechend weiter läuft.
4) Nach vollständigem Empfang des Paketes wartet der Empfänger ein SIFS, bevor das ACK gesendet wird.
5) Eine Kollision durch gleichzeitigen Ablauf des Backoffs führt zu einem ACK-Timeout – nach welchem ein EIFS gewartet wird bevor sich der gesamte Vorgang wiederholen kann ( DIFS → BO .. ).


ARP
----------

IP-Adresse --> MAC-Adresse
### Host A
1) Der lokale **ARP-Cache** wird überprüft:  
   *Kenne ich die MAC-Adresse zu dieser IP-Adresse schon?*
2) **ARP-Request** wird per MAC-Broadcast (Adresse: FF-FF-FF-FF-FF-FF) verschickt  
   *Wer hat diese IP-Adresse?*
### Host B
3) Empfang des Broadcasts
4) Zuordnung IP- & MAC-Adresse von Host A im eigenen ARP-Cache  
   (macht die zukünftige Kommunikation effizienter)  
5) **ARP-Reply**
### Host A
6) Zuordnung IP- & MAC-Adresse von Host B im eigenen ARP-Cache