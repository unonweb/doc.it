

RAM
=======

* Das speichernde Element ist dabei ein Kondensator, der entweder geladen oder entladen ist. Über einen Schalttransistor wird er zugänglich und entweder ausgelesen oder mit neuem Inhalt beschrieben. 

### Leistung

Es kommt auf das Verhältnis zwischen Geschwindigkeit und Latenz an. 
* Schritt 1: Bestimmen Sie die höchste Speichergeschwindigkeit, die sowohl von Ihrem Prozessor als auch von Ihrem Motherboard unterstützt wird (einschließlich übertakteter Profile).
* Schritt 2: Wählen Sie den Speicher mit der niedrigsten Latenz, der mit dieser Geschwindigkeit zu Ihrem Budget passt, und denken Sie daran, dass eine bessere (d. h. niedrigere) Latenz eine höhere Systemleistung bedeutet.

#### Berechnung

FSB ist 2666 MHz und PC4 21333 hängen mit Faktor 8 zusammen.
`absolute Latenz in Sekunden = (2 * Timing) / Geschwindigkeit`


`Datenrate = Speichertakt * Busbreite * Faktor`
* Speichertakt: 
* Busbreite: 32Bit | 64Bit
* Faktor: sdr/ddr

#### Aufgaben

* Berechnen Sie die maximale theoretische Übertragungsrate Ihrer ausgewählten RAM Speichermodule (DDR 3-1333).
  *  1333 MHz * 64 = 85312 Mbit/s /8 --> 10664 MB/s

#### Betriebsart: Dual Channel

Dual-Channel-Betrieb. Wenn ihr über zwei RAM-Module mit identischen Spezifikationen verfügt (Takt, Bauart, Kapazität etc.), dann kann der Prozessor die beiden Riegel zusammen schneller ansprechen. Dazu müsst ihr die Riegel nur in zwei gleichfarbige Slots stecken, der Rest der Magie passiert ganz von allein.

### Parameter

* *DRAM-Frequency* / *Speichertakt*: Diesen Wert müssen Sie verdoppeln, um den richtigen Speicher beim Händler zu finden.
  * 666 MHz bedeutet, dass ein Speicher mit 1.333 MHz benötigt wird (DDR3-1333)

`8GB 2Rx8 PC3L 12800S`

* `2R` # die Größe der Blöcke, auf die zugegriffen wird
  * `1R` # *Single-Rank-Modul*: 64 Bits große Blöcke
  * `2R` # *Dual-Rank-Modul*: 128 Bits große Blöcke
  * `4R` # *Quad-Rank-Modul*: 256 Bits große Blöcke
* `x8` bedeutet, dass dieses Modul 8 verschiedene Speicherbänke pro einzelnen Chip hat
* `PC3L` # die Bauart; *L* steht für *Low Power*
  * PC3 (DDR3)
  * PC4 (DDR4)
* `12800S` # Maximalgeschwindigkeit (12800MB/s, also 12,8GB/s) (burst rate)

#### 2R --> Rank
Rank is a data block which is 64 bits wide without Error Correction Code (ECC) created using some, or all of the memory chips on a module.

#### PC3 --> Bandwith
There is also an industry name that indicates the theoretical bandwidth of the module, for example "`PC3-6400`".  
Bandwidth is calculated by taking the transfers per second and multiplying by eight  
(DDR3 transfers data on a bus that is 64 bits wide, and because a byte is eight bits, this is eight bytes of data per transfer).

The total bandwidth is the product of: 
* Base DRAM clock frequency
* Number of data transfers per clock: *Two*, in the case of "double data rate" (DDR, DDR2, DDR3, DDR4) memory.
* Memory bus (interface) width: Each DDR, DDR2, or DDR3 memory interface is *64* bits wide. Those 64 bits are sometimes referred to as a "line."
* Number of interfaces: Modern personal computers typically use two memory interfaces (dual-channel mode) for an effective 128-bit bus width.

`clocks per second) * lines per clock * bits per line = bits per second`  
bits per second / 8     
= bytes per second  
bytes per second / 1.000.000  
= Megabytes per second  

* DDR2-800: *800.000.000* clocks/s * *2* lines/clock * *64* bits/line = *102.400.000.000* Bits/s
* DDR2-800: *800* MHz * *64* bits/line = *102.400* MegaBits/s : *8* = *15050* MegaBytes/s
* DDR2-800: *400* MHz * *2* lines/clock * *64* bits/line = *102.400* MegaBits/s : *8* = *15050* MegaBytes/s
* `DDR4-2666`: *2666* Mhz * *64* Bits/Line = *170624* Bits/s : *8* = *21328* Bytes/s --> `PC4-21300`
* `DDR4-667`: *667* Mhz * *64* Bits/Line = *42688* Bits/s : *8* = *5336* Bytes/s --> `PC4-5300`

### Timingparameter
Die Timings des neuen Speichers sollten nicht von denen abweichen, mit denen ihr bereits eingebauter Speicher arbeitet. Bei PCs können Sie die Parameter im BIOS in einem gewissen Rahmen nachjustieren, Notebooks reagieren dagegen sehr empfindlich auf Timinig-Abweichungen - mit Abstürzen und Instabilität.

`CL12-34-56`  
`(CL)(tRCD)(tRP)(tRAS)`

1. CL
   * for asynchronous DRAM specified in nano seconds 
   * for synchronous DRAM specified in clock cycles.
   * A lower CAS Latency Ratio is always better
2. tRCD
3. tRP
4. tRAS





#### CL / Speicherlatenz
*Column Address Strobe Latency* (englisch, kurz CL oder CAS Latency), auch Speicherlatenz  
ist die benötigte Zeit um eine Spalte im Hauptspeicher eines Computers zu adressieren.  
Das Maß dafür ist die erforderliche Zahl der Taktzyklen. Je kleiner diese Zahl desto besser
* Die beschleunigende Wirkung von geringeren CL-Timings wird jedoch gewöhnlich überschätzt. Sie liegt im Allgemeinen unter 5 % und gilt damit als für den Anwender nicht wahrnehmbar.
* Die enttäuschende Beschleunigungswirkung lässt sich hauptsächlich durch die immer wirksameren und größeren Caches auf den Prozessoren erklären, die bereits ca. 90–95 % aller Zugriffe abfangen.





Upgrading / Mixing
------------------------

The following specs have to match:
* DDR generation
* CAS latency
* timings
* voltage

If the timings are the same, the frequency doesn't have to match. It's going to be reduced to the slowest member.