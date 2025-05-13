---
title: Datenübertragungsrate
---


Datenübertragungsrate
---------------------------

* Bezeichnet die digitale Datenmenge, die innerhalb einer Zeitspanne über einen Übertragungskanal übertragen wird.
* *brutto* (inkl. Steuerdaten (Overhead))
* Da die kleinste Einheit der Datenmenge das Bit ist, wird die Übertragungsrate häufig auch als *Bitrate* bezeichnet.
* Bei Datenübertragungsraten werden die *dezimalen Einheitenvorsätze* (Präfixe) verwendet; keine Binärpräfixe
* Bei paralleler Datenübertragung (vor allem beim Zugriff auf Datenspeicher über einen Datenbus), wird die Übertragungsrate auch häufig in *Byte/s* angegeben. Man muss also darauf achten, ob eine Übertragungsrate z. B. mit 1 MB/s oder mit 1 Mbit/s angegeben wird.

#### Synonyme
* Datenübertragungsrate
* Datentransferrate
* Datenrate
* Bitrate
* Übertragungsgeschwindigkeit
* Verbindungsgeschwindigkeit

#### andere Größen
* Bandbreite
* Kapazität
* *Datendurchsatz*: gibt die Netto-Datenmenge pro Zeit an (nur Nutzdaten)
* *Kanalkapazität*: Die maximal mögliche Datenübertragungsrate, die fehlerfrei über einen Kanal übertragen werden kann 


Datenübertragungsrate vs Datendurchsatz
---------------------------------------------

Technik         |Brutto Mbit/s  |Brutto Mbyte/s |Netto Mbit/s   |Netto Mbyte/s
----------------|---------------|---------------|---------------|---------------
Fast-Ethernet   |100            |               |94             |11,7
Gigabit-Ethernet|1000           |               |940            |117
USB 2.0         |480            |               |bis 280        |
USB 3.0         |5000           |               |3,84 Gbit/s    |480 
USB 3.1         |10000 	        |               |7,2 Gbit/s     |> 900
USB 4.0         |40000          |4.800


Fast-Ethernet
----------------

* Theorie: Datenrate von *100 MBit/s* (= *12,5 Mbyte/s*) 
* Praxis: *11,7 MByte/s*


Gigabit-Netzwerk
------------------

* Theorie: *1000 MBit/s*  (= *125 MByte/s*)
* Praxis: höchstens *936 MBit/s* (= *117 MByte/s*)

#### Kabel
* Alle nach 2003 produzierten und verkauften Cat-5-Kabel sind automatisch Cat-5e-Kabel und somit Gigabit-tauglich.
  (Ein Kabel ist also Gigabit-fähig, wenn auf dem Mantel *Cat 5e* oder auch, wenn *Cat 5* steht, das Kabel aber nach 2003 produziert und gekauft wurde.)
* Cat-5-Kabel sollten nicht länger als *100 Meter* sein.


Datenübertragungsraten im Verhältnis
-----------------------------------------

2016: Selbst die schnellsten Festplatten (315 MByte/s) werden durch aktuelle SATA-Schnittstellen nicht ausgebremst (600 MByte/s). Die SATA-Schnittstelle stellt also für Festplatten keinen Flaschenhals dar, ganz im Gegensatz zu SSDs, die bei Anbindung per PCI Express mittlerweile mit 2000 bis 5000 MByte/s drei- bis achtmal schneller als die schnellste SATA-Schnittstelle sind. 

#### Festplatte

* 2018: 
  * Festplatten: Datenraten von im Schnitt *150 MByte/s* beim Schreiben und Lesen.
  * SSDs: *600 MByte/s* lesen
* 2006:
  * Festplatten: Datenraten von *60 MByte/s* beim Schreiben und Lesen galten als gut. Dies entspricht gerade einmal der Hälfte der Datenrate, die ein Gigabit-Netzwerk zu leisten vermag! Da Fast-Ethernet jedoch bei ca. *12 MByte/s* liegt, würde sich die Geschwindkeit mit Gigabit-Ethernet immer noch um den Faktor *5* erhöhen (statt Faktor 10).  
