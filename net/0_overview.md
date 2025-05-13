---
title: Overview
---


OSI-Modell
===========

1. **Physical Layer** (Bitübertragungsschicht): 
   * Definiert wie die physikalische Übertragung der Daten über eine Leitung stattfindet.
   * Definition der elektrischen Spezifikationen der Leitung/des Kabels und der Stecker
2. **Data Link Layer** (Sicherungsschicht):
   * Definition der Zugriffsmethoden auf das Kabel
   * Bündelung der Daten zu Paketen
   * Hinzufügen von *Header-* und *Trailerinformationen*, die zur Weiterleitung benötigt werden.
   * Transport der Daten von Station zu Station
   * Fehlerkontrolle
   * ARP
3. **Network Layer** (Vermittlungsschicht):
   * Routing: Adressierung innerhalb und zwischen Netzwerken / Festlegung/Ermittlung von Routen zur Datenübertragung
   * Weiterleitung der Datenpakete unter Zuhilfenahme von Schicht 2
   * IP, NAT
4. **Transport Layer**:
   * Sichert die fehlerfreie Übertragung der Daten: Sind alle Pakete vollständig angekommen?
   * TCP, UDP
5. **Session Layer**:
   * Stellt eine logische Verbindung zwischen den Prozessen her, die auf dem Server und dem Client laufen
   * Aufbau der Sessionverbindung
   * Kontinuierlicher Wechsel von Anfragen/Antworten zwischen den Anwendungen
6. **Presentation Layer** (Darstellungsschicht):
   * Umwandlung der Daten in das jeweils erforderliche Format (wird von der Anwendung bestimmt)
7. **Application Layer** (Anwendungsschicht):
   * DNS
   * HTTP

# DoD-Schichtenmodell

* https://de.wikipedia.org/wiki/Internetprotokollfamilie#TCP/IP-Referenzmodell

| OSI-Schicht        | TCP/IP-Schicht | Beispiel                                                     |
| ------------------ | -------------- | ------------------------------------------------------------ |
| Anwendungen (7)    | Anwendungen    | [HTTP](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol), [UDS](https://de.wikipedia.org/wiki/Unified_Diagnostic_Services), [FTP](https://de.wikipedia.org/wiki/File_Transfer_Protocol), [SMTP](https://de.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol), [POP](https://de.wikipedia.org/wiki/Post_Office_Protocol), [Telnet](https://de.wikipedia.org/wiki/Telnet), [DHCP](https://de.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol), [OPC UA](https://de.wikipedia.org/wiki/OPC_Unified_Architecture)   [TLS](https://de.wikipedia.org/wiki/Transport_Layer_Security), [SOCKS](https://de.wikipedia.org/wiki/SOCKS) |
| Darstellung (6)    | Anwendungen    |                                                              |
| Sitzung (5)        | Anwendungen    |                                                              |
| Transport (4)      | Transport      | [TCP](https://de.wikipedia.org/wiki/Transmission_Control_Protocol), [UDP](https://de.wikipedia.org/wiki/User_Datagram_Protocol), [SCTP](https://de.wikipedia.org/wiki/Stream_Control_Transmission_Protocol) |
| Vermittlung (3)    | Internet       | [IP](https://de.wikipedia.org/wiki/Internet_Protocol) ([IPv4](https://de.wikipedia.org/wiki/IPv4), [IPv6](https://de.wikipedia.org/wiki/IPv6)), [ICMP](https://de.wikipedia.org/wiki/Internet_Control_Message_Protocol) (über IP) |
| Sicherung (2)      | Netzzugang     | [Ethernet](https://de.wikipedia.org/wiki/Ethernet), [Token Bus](https://de.wikipedia.org/wiki/Token_Bus), [Token Ring](https://de.wikipedia.org/wiki/Token_Ring), [FDDI](https://de.wikipedia.org/wiki/Fiber_Distributed_Data_Interface) |
| Bitübertragung (1) | Netzzugang     |                                                              |


Übersicht
============

### remote

#### Fall 1

**IP** --> Berechnung Netzwerk-ID. Ist das Ziel im selben Subnetz? --> ja --> **ARP** --> Kenne ich seine MAC-Adresse schon (ARP-Cache)? --> nein --> ARP-Request (MAC-Broadcast) --> ARP-Reply

### lokal

#### Fall 2

Host A: `192.168.50.11/24` --> Netzwerk-ID: `192.168.50.0`  
Host B: `192.168.51.78/24` --> Netzwerk-ID: `192.168.51.0`

* **Host A**  
  *IP* --> Berechnung Netzwerk-ID. Ist das Ziel im selben Subnetz? --> nein --> *ARP* --> ARP-Cache: Kenne ich die MAC-Adresse des Standard-Gateways? --> nein --> ARP-Request: MAC-Broadcast --> ARP-Reply --> Datenübertragung an das Gateway (IP-Adresse: 192.168.51.78 / MAC-Adresse des Gateways)
* **Router A**: Ist das Ziel-IP-Adresse in einem angeschlossenen Subnetz? --> nein --> Routing Tabelle: Kenne ich einen einen Weg zum Ziel? --> nein --> Standard-Gateway
* **Router B**: ... 

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


Hardware
-------------

### Netzwerkkarte

Schnittstelle zwischen Computer und Kabel

* Empfangen der Daten vom Computer & Umwandeln in elektrische Signale
* Empfangen der elektrischen Signale & Umwandeln in Daten
* Prüfung des Adressaten (*MAC*)
* Steuerung des Datenflusses durch das Kabel (*Ethernet* oder *Token Ring*)

### Switch

* Funktion eines Hubs + MAC-Adressen zu lesen und zu speichern
* Switches brechen die Ethernet-Busstruktur in eine Bus-/Sternstruktur auf. Teilsegmente mit Busstruktur werden sternförmig über je einen Port des Switch gekoppelt.

* Die Datenlast sollte nach Möglichkeit gleichmäßig über die Ports verteilt werden. Systeme, die viele Daten übertragen, müssen unter Umständen an einen eigenen Switch-Port angeschlossen werden (Private Ethernet)
* Systeme die viel miteinander kommunizieren, an einen gemeinsamen Port anschließen, um so die Datenmengen, die mehr als ein Segment durchlaufen müssen, zu reduzieren. 

Wenn ein Switch ein Datenpaket empfängt, liest er die MAC-Adresse des Senders aus und verknüpft diese in der MAC-Adress-Table mit dem Port auf dem er das Paket empfangen hat: `Port 1: <MAC-Adresse>`. Kommt die Antwortet, weiß der Switch schon hinter welchem Port der Empfänger wartet und muss somit nicht mehr auf allen Ports senden.

* Kollisionen innerhalb des Switches werden durch dedizierte Leitungen vermieden. Dadurch entfällt die Notwendigkeit der Netzwerkkarte auf einem der Drähte nach Kollisionen zu lauschen. Es kann somit auf **Full Duplex** geschaltet werden.

#### Troubleshooting

* Port-Statistiken


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


Layer 3
==================


* Hardware: Router

IP
--------

### Primäre Aufgabe
* Ist das Ziel lokal oder remote?  
  --> Netzwerk-ID berechnenen

#### Zur Verfügung stehende Informationen

* eigene IP-Adresse
* eigene Subnetzmaske
* remote IP-Adresse
* remote Subnetzmaske

### Berechnung der Netzwerk-ID

<pre>
* lokal
  * IP-Adresse:       192.168.1.100
  * Subnetzmaske:     255.255.255.0
  --> Netzwerk-ID:    192.168.1.0
* remote
  * IP-Adresse:       192.168.2.10
  * Subnetzmaske:     255.255.255.0
  --> Netzwerk-ID:    192.168.2.0
</pre>

--> Wo die Subnetzmaske den Wert **255** hat, bleibt die IP-Adresse, wie sie ist.  
--> Wo die Subnetzmaske den Wert **0** hat, wird die IP-Adresse auch 0.


IP-Adressen
--------------

#### Binär
`XXXXXXXX.XXXXXXXX.XXXXXXXX.XXXXXXXX.`
#### Dezimal
`0-255.0-255.0-255.0-255.`

* Vier Zahlenblöcke
* Zahlenblock = Oktett = 1 Byte
* 11111111 = 255

### CIDR

**Classless Inter-Domain Routing**
* Das Suffix gibt die Anzahl der 1-Bits in der Netzmaske an.

`/8`  --> `255.0.0.0`  
`/16` --> `255.255.0.0`  
`/24` --> `255.255.255.0`  

`/25` --> `255.255.255.128` 

### Klassen & Private Adressen

* Unterscheidung anhand des ersten Bytes

#### Klasse A
* Adressen: `1-126.X.Y.Z`
* Subnetz: `255.0.0.0`
* Netzwerk/Host: `N.H.H.H`
* Privat: `10.0.0.0` - `10.255.255.255`
* Anzahl Netze: 126
* Anzahl Hosts: 16 Millionen
* Besondere Adressen: 
  * `127.0.0.1` # Loopback

#### Klasse B
* Adressen: `128-191.X.Y.Z`
* Subnetz: `255.255.0.0`
* Netzwerk/Host: `N.N.H.H`
* Privat: `172.16.0.0` - `172.31.255.255`
* Anzahl Netze: 16 Tausend
* Anzahl Hosts: 65 Tausend

#### Klasse C
* Adressen: `192-223.X.Y.Z`
* Subnetz: `255.255.255.0`
* Netzwerk/Host: `N.N.N.H`
* Privat: `192.168.0.0` - `192.168.255.255` bzw. 192.168/16
* Anzahl Netze: 2 Millionen
* Anzahl Hosts: 254 Hosts

#### Klasse D
* Adressen: `224-239.X.Y.Z`
* Subnetz: `255.255.255.255`
* Netzwerk/Host: `N.N.N.N`
* Anzahl Netze: 15
* Anzahl Hosts: -
* Verwendung für **Multicastadressen**

#### Klasse E
* Adressen: `240-247.X.Y.Z`
* Subnetz: `255.255.255.255`
* Netzwerk/Host: `N.N.N.N`
* Anzahl Netze: 7
* Anzahl Hosts: -

#### Private Adressen
* **10.0.0.0/8** (10.0.0.0 bis 10.255.255.255):  
  Reserviert für die Nutzung in privaten Netzwerken.
* **172.16.0.0/12** (172.16.0.0 bis 172.31.255.255):  
  Reserviert für die Nutzung in privaten Netzwerken.
* **192.168.0.0/16** (192.168.0.0 bis 192.168.255.255):  
  Reserviert für die Nutzung in privaten Netzwerken.
* **169.254.0.0/16** (169.254.0.0 bis 169.254.255.255):  
  Link-local Adresses für IPv4LL.

### Fragen & Berechnungen

#### Wie viele Rechner gehören maximal zu einem Netz?
--> kann mit Hilfe der Subnet-Mask beantwortet werden.

Beispiel: Ein Netzwerk hat die Subnet-Mask 255.255.255.0. Für die Host-ID verbleiben 8 Bit. Somit gibt es zunächst 2^8 = 256 Möglichkeiten für die Host-ID. Die Bits der Host-ID dürfen weder alle 1 noch alle 0 sein. Somit verbleiben 254 Möglichkeiten für die Host-ID. 




Layer 4
===============


TCP
-----------

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
----------

* verbindungslos
* keine Session
* unzuverlässig
* schnell
* Verwendung: Broadcasts 

* due to nature of how UDP works, it's not possible to test if a remote udp port is available; there's no response in UDP communication.

UDP is a connectionless protocol that runs on top of IP (UDP/IP), and TCP is a connection-oriented protocol that runs on top of IP (TCP/IP). Connectionless means that a host can send a message to another host without first establishing a connection with the recipient. The host simply puts a message onto the network with a destination address and hopes that the message arrives. In addition, the transmission or receipt of a UDP packet doesn't guarantee any further communication in either direction.


Anwendungsprotokolle
=======================


* Protokolle an die TCP/UDP die Daten übergeben


Ports
--------

* Schnittstellen zwischen Transportprotokollen und Anwendungsprotokollen
* Endpunkte der TCP/UDP-Kommunikation (definierte Übergabevereinbarung)
* Es gibt verschiedene Ports für TCP und UDP, wobei häufig diesselben Nr. verwendet werden 

#### Well know ports

Ports 0-1023

## DHCP

kommuniziert mittels UDP in Schicht 4, implementiert jedoch ein eigenes Protokoll, welches nichts mit der Netzwerkkommunikation an sich zu tun hat – damit fällt DHCP in Schicht 7


DNS
------------

### Reihenfolge der Namensauflösung
1) Local Host Name
2) Datei *Hosts*  
   --> `C:\Windows\System32\drivers\etc\hosts`
3) DNS-Server
4) NetBIOS-Namenscache
5) WINS
6) Broadcast
7) Datei *LMHosts* (nur NetBios-Computer)

#### Forward Lookup Zone

Name --> IP-Adresse  
(Telefonbuch)

#### Reverse Lookup Zone

IP-Adresse --> Name  
(für Kontrolle und Tests)

### Zonentyp

#### Primäre Zone

* Speichert Zuordnungen in einer Textdatei lokal auf dem Server

#### Sekundäre Zone

* Zweck: Lastenverteilung, Fehlertoleranz
* Keine Änderung der Daten möglich (Schreibschutz)

#### Stubzone


#### Zone in Active Directory speichern

* Der DNS-Server muss sich auf einem Domänencontroller befinden


HTTP
----------

see extra doc


LLMNR
---------

**Link Local Multicast Name Resolution** ist ein Protokoll basierend auf dem DNS-Paketformat, das IPv4 und IPv6 Hosts die Namensauflösung für Hosts im selben lokalen Netzwerk erlaubt.

* Unter Linux ist es im *systemd-resolved* implementiert.

Um auf Anfragen zu antworten, lauschen Empfänger auf **UDP-Port 5355** in den folgenden Multicast-Adress-Bereichen:

* IPv4 – `224.0.0.252`, MAC-Adresse – `01-00-5E-00-00-FC`
* IPv6 – `FF02::1:3`, Ethernet Multicast Adresse – `33-33-00-01-00-03`

Die Empfänger lauschen auch auf **TCP-Port 5355** auf der Unicast-Adresse, welche der Host zum Antworten nutzt. 

Protokolle
=============

* Art der Codierung der Daten
* Größe der Datenrahmen
* Art der Übertragung

#### Inhalt

* Sender
* Empfänger
* Sonstige Protokolle
* Daten

Die eigentliche Datenübertragung ist nur ein kleiner Teil der Kommunikation.


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
