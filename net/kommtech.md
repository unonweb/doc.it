---
title: Kommunikationstechnik
---

Kommunikationsarten
---------------------

### Unicast

* Gezielte Kommunikation zwischen zwei definierten Adressen.
* Es kann immer nur eine Adresse als Ziel angegeben werden. Daten für mehrere Empfänger müssen getrennt verschickt werden.

### Broadcast

* "Rundsendung" ohne definierte Zieladresse.
* Viele Broadcasts belasten und verlangsamen Netzwerke.
* Wird nicht geroutet

### Multicast

* Sendung an eine vorher definierte Multicastgruppe


KOMMUNIKATIONSTECHNIK
=======================

### Duplex
= die **Richtungsunabhängigkeit eines Kommunikationskanals**.

#### Übersicht

Bezeichnung |Kürzel |Beschreibung | Beispiele
----------- |------ |------------ |------------
Simplex|SX|Daten können in nur eine Richtung übertragen werden, keine Antwort möglich| Rundfunk, Pager
Halbduplex/Wechselbetrieb|HX oder HDX|Daten können abwechselnd, aber nicht gleichzeitig, in beide Richtungen fließen|Wechselsprechanlage, CB-Funk, USB bis 2.0
Vollduplex/Gegenbetrieb|DX oder FDX|Daten können in beide Richtungen gleichzeitig übertragen werden.|Gegensprechanlage, POTS, GSM, USB ab 3.0
Dual-Simplex|DSX|ähnlich Vollduplex, aber getrennte Sende- und Empfangswege|PCI Express, Serial ATA

#### Duplex-Verfahren

Werden Informationstransfers in beide Richtungen auf demselben Medium vorgenommen, müssen die Informationen durch Duplex-Verfahren zusammengeführt und getrennt werden. 

* **Zeitduplex** (time division duplex, TDD): Hierbei nutzen Sende- und Empfangskanal die gleiche Frequenz, sind aber zeitlich voneinander getrennt. Die Informationen werden mit Hilfe eines festgelegten Zeitgebers in kurzen Sequenzen zeitversetzt übertragen. Durch die Digitalisierung des Sprachsignals können die Sprachsignale in Blöcke zusammengefasst übertragen werden. Trotzdem besteht auf der analogen Ebene eine kontinuierliche Sprechverbindung in beiden Richtungen. Verwendung: DECT-Telefone
* **Frequenzduplex** (frequency division duplex, FDD): Die Informationen werden für jede Richtung mit Hilfe einer anderen Trägerfrequenz übertragen (vgl. Bandlage). Dies kann mit einer Duplexweiche technisch realisiert werden. Es ermöglicht, dass ein Gerät gleichzeitig senden und empfangen kann.
Frequenzduplex ist für historische, analoge Verfahren zur Funktelefonie, z. B. im A-Netz, B-Netz und C-Netz in Deutschland typisch.
In der Satellitenkommunikation wird nach Uplink- und Downlink-Frequenz unterschieden, die sogar in unterschiedlichen Frequenzbändern liegen können

### Multiplex

Multiplexverfahren (lat. multiplex „vielfach, vielfältig“) sind Methoden zur Signal- und Nachrichtenübertragung, bei denen mehrere Signale zusammengefasst (*gebündelt*) und simultan über ein Medium (Leitung, Kabel oder Funkstrecke) übertragen werden. Oftmals werden Multiplexverfahren auch kombiniert, um eine noch höhere Nutzung zu erreichen. 
Bündelung/Multiplexing erfolgt, nachdem die Nutzdaten auf ein Trägersignal moduliert wurden. 
Entsprechend werden sie beim Empfänger nach der Entbündelung/Demultiplexen demoduliert. 

### Termini

* **Datendurchsatz** (goodput): gibt die Netto-Datenmenge pro Zeit an, die über ein kabelgebundenes oder kabelloses Netz übertragen werden kann. Beim Datendurchsatz werden *die reinen Nutzdaten* berücksichtigt. Bei der Datenübertragungsrate hingegen werden eventuelle Steuerdaten (englisch: Overhead) mitgerechnet.
* **Datenübertragungsrate** (Datentransferrate, Datenrate): bezeichnet die digitale Datenmenge, die innerhalb einer Zeitspanne über einen Übertragungskanal übertragen wird. Synonym werden auch die Begriffe Datenübertragungsgeschwindigkeit, Übertragungsgeschwindigkeit, Verbindungsgeschwindigkeit verwendet. Da die kleinste Einheit der Datenmenge das Bit ist, wird die Übertragungsrate häufig auch als Bitrate bezeichnet. 

Beim 2006 eingeführten WLAN-Standard 802.11n (Stand: August 2012) geht man von einem Datendurchsatz von 40 % der maximalen Datenübertragungsrate aus. Es können dementsprechend von theoretisch möglichen 600 Mbit/s maximal 240 Mbit/s in der Praxis erreicht werden. Diese 600 Mbit/s brutto bei 802.11n erreicht ein Router im 5-GHz-Band mit vier Antennen und einer Kanalbandbreite von 40 MHz. Als weitere Voraussetzungen müssen sowohl Sender als auch Empfänger jeweils vier Antennen haben und zwischen ihnen muss die Funkverbindung „gut“ sein.

**1 MB/s** vs **1 Mbit/s**: erstere Angabe entspricht exakt dem Achtfachen der Geschwindigkeit der letzteren