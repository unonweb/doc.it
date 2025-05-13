---
title: Einheiten
---


Präfixe / Einheitenvorsätze
-----------------------------

* Windows zählt zum Beispiel den Speicher binär, während viele Festplatten-Hersteller auf das Dezimalpräfix setzen. 
* MiB   (Mebibyte) = 1.024 KiB (Kibibyte)
* MB    (Megabyte)


### Binärpräfixe

Name    |Symbol |Wert
--------|-------|----------
kibi    |Ki     |$2^{10} = 1024^1 = 1.024$
mebi 	  |Mi     |$2^{20} = 1024^2 = 1.048.576$
gibi 	  |Gi    	|$2^{30} = 1024^3 = 1.073.741.824$

### Umrechnung Binärpräfix <> Dezimalpräfix

* Dezimal > Binär. Beispiel 500 Mb$\frac {5000}{1000} > \frac {5000}{1024}$  
  (Wenn man durch einen größeren Nenner teilt, wird das Ergebnis kleiner)
1. Grundeinheit berechnen --> Faktor
2. Multiplikation mit der gegebenen Anzahl

Dezimal --> Binär           |Binär --> Dezimal
----------------------------|-----------------------------
$1 Kbyte = 1,024^{-1}Kibyte$|$1 Kibyte = 1,024^{1}Kbyte$|
$1 Mbyte = 1,024^{-2}Mibyte$|$1 Mibyte = 1,024^{2}Mbyte$|
$1 Gbyte = 1,024^{-3}Gibyte$|$1 Gibyte = 1,024^{3}Gbyte$|

* 1 KB entspricht 0.976 KiB ($1,024^{-1}$)
* 1 MB entspricht 0,953 MiB ($1,024^{-2})
* 1 GB entspricht 0,931 GiB ($1,024^{-3})

### Beispiele

* Umrechnung von Mbit/s in MB/s:  
  8 Mbit/s = 1 MB/s ≈ 0,95 MiB/s.

#### Wie viele Bit haben 4 Gibibyte (GiB)?
* $4*1024^3 = 4.294.967.296$   
   $alternativ: 4*2^{30}$  
* $8* 4.294.967.296 = 34.359.738.368$