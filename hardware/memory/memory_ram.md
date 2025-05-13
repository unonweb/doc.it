## Arbeitsspeicher / Festspeicher

* RAM access data in the order of nanoseconds (RAM = random access memory)
* SSD access data in microseconds (SSD = solid state drive)
* HDD accesses the data in milliseconds (HDD = hard disk drive)

This means that RAM is 1000 times faster than SSD and 100,000 times faster than the usual HDD.

##  suspend / hibernate

***Suspend*** (*Bereitschaft?*) puts the computer to sleep by saving system state in RAM. In this state the computer goes into a low power mode, but the system still requires power to keep the data in RAM. To be clear, Suspend does not turn off your computer (Suspend-to-RAM).

***Hibernate*** (*Ruhezustand?*) is a bit of a deeper sleep. Hibernate mode moves the current contents of memory into swap space. In this state the computer does not require power. To be clear, the computer is completely powered off with Hibernate (=Suspend-to-Disk).

# RAM

## Ablauf

* The CPU processes the datasets supplied by cache memory, known as *L1*, *L2* & *L3* cache.
* When required dataset not available with these caches then the processor orders to *memory controller* to fetch that dataset from RAM.
* The memory controller works as the middleman between processor and RAM.
* Now memory controller forwards this request to RAM.
* RAM processes’ the request and produce the output to memory controller.
* This delay between requesting & receiving the dataset is nothing but the *CAS latency* or *CL ratio* of that RAM.

## Taktfrequenz / Clock Rate

Die Taktfrequenz bezeichnet die Anzahl der Arbeitsschritte pro Sekunde.

* Die **interne Taktrate**, auch *CPU-Takt* genannt, bezeichnet das Tempo, mit dem die CPU intern die Befehle abarbeitet: je mehr Megahertz, desto schneller ist die jeweilige CPU. 
* Die **externe Taktrate**, auch *Systemtakt* oder *Front Side Bus* (FSB) genannt, legt fest, mit welcher Geschwindigkeit der Prozessor auf den Arbeitsspeicher (ggf. auch auf den den Second-Level-Cache) zugreift. Der Systemtakt wird vom Motherboard vorgegeben.

## Bauart / Generation

* *SDRAM*: *Synchronous Dynamic Random Access Memory* (heute die Regel). SDRAM is a generic term for much older pre-DDR RAM technology, with most systems that use this type of RAM since retired.

[ramcity](https://help.ramcity.com.au/hc/en-us/articles/360001143896-What-are-the-different-Memory-RAM-types-)

### SRAM vs. DRAM vs. ECC
*Static random access memory* (SRAM) and *dynamic access memory* (DRAM) are two classifications of memory.  

#### DRAM
* less expensive to produce, but is slightly slower than SRAM. Most user-replaceable memory modules are DRAM.
* *Error-correcting code* (ECC) is a type of DRAM that has an additional cell to detect and correct random faults.

#### SDR SDRAM
*Single data rate* (SDR) SDRAM 

* is an older type of memory, commonly used in computers prior to 2001
* Typical clock frequencies are *100Mhz* and *133MHz*.
  SDR SDRAM modules are usually made in a *168-pin* form factor for desktops, and a *144-pin* form factor for notebook applications.  


#### DDR SDRAM
*Double data rate* (DDR) SDRAM hit the mainstream computer market around 2002 and is a straightforward evolution from SDR SDRAM.  
The most significant difference between DDR and SDR is that DDR uses '*double pumping*' (transferring data on both the rising and falling edges of the clock signal) to allow data transfer rates that are double that of the earlier SDR SDRAM technology.  

DDR SDRAM modules for desktop computers have *184 pins*
DDR2 SDRAM: *240 pins*

#### DIMM
*Dual Inline Memory Module*. Heißt nichts anderes als dass die goldenen Kontakte an der Unterseite eines RAM-Moduls auf den zwei Seiten verschiedene Signale übertragen, also nicht elektrisch leitend miteinander verbunden sind. Die ersten RAM-Module waren sogenannte SIMM’s, also *Single Inline Memory Modules*, bei denen die Kontakte auf Vorder- und Rückseite miteinander verbunden waren.

#### SO-DIMM
*Small Outline Dual Inline Memory Module*
Solche Module sind meist sparsamer und wesentlich kleiner als herkömmlicher RAM für PCs und werden in Notebooks und sehr kleinen Computern eingesetzt. Auch sind durch die kleinere Bauform weniger Kontakte vorhanden.

## DDR-Generation

* **DDR4** (Double Data Rate 4)
  * 2133Mhz - 3200Mhz
  * 1.2V/Module
* Es ist nicht möglich DDR4 RAM in einen DDR3 Slot (oder andersherum) zu verbauen, da der Notch woanders liegt.

## Example

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
## Kompatibilität

1. **Generation:** Da die Einkerbungen bei jedem Arbeitsspeicher an unterschiedlichen Stellen liegen (die für den Einbau wichtig sind), sind die verschiedenen Arbeitsspeichertechnologien nicht miteinander kompatibel. Motherboards  unterstützen in der Regel nur eine Arbeitsspeichertechnologie.
   * DDR3 (2007)
   * DDR4 (2014)
   * DDR5
2. **Form-Faktor:** 
   - SODIMM
   - DIMM
3. **Geschwindigkeit:** 
   - Taktfrequenz des RAM wird in MHz (Frequenz) oder als Datenrate in Megatransfers pro Sekunde (MT/s)
   - Das Speicherkonsortium JEDEC hat die Taktraten DDR-400, DDR2-1066, **DDR3-2133** und DDR4-3200 als offizielle Höchstwerte für die  unterschiedlichen DDR-Generationen festgesetzt.

### Spannung

Vermeiden Sie, RAM mit einer zu hohen Standardspannung zu erwerben. Die Standardwerte von **1,2** Volt (DDR4-RAM), 1,5 Volt (DDR3-SDRAM), **1,8 Volt**  (DDR2-SDRAM) oder 2,5 Volt (DDR-SDRAM) reichen für den stabilen Betrieb  von Standard-RAM aus.

### unbuffered

In normalen Desktop-PCs und Notebooks wird "unbuffered", "unregistered"  RAM ohne ECC verbaut. Hinter den Begriffen "fully buffered", ECC und  "registered" stecken Techniken, die typischerweise für Servern im  professionellen Umfeld gedacht sind/waren, also dort wo sehr große  Speichermengen verbaut sind. Dort helfen sie, die Datenintegrität zu  gewährleisten, wo Rechner rund um die Uhr stark ausgelastet sind und ein kleiner Fehler zu hohen Folgekosten führen kann. Für den Hausgebrauch  sind solche RAM-Sticks nicht gedacht und auch nicht notwendig.