# SSD

* Flashmedium
* praktisch ein RAID 0 mit vielen USB-Sticks
* Anschluss per SATA (der SATA-Controller = Flaschenhals; verringert die Geschwindigkeit enorm)

Schreibzugriffe auf eine einzelne Speicherzelle sind begrenzt; eine abgenutzte Speicherzelle wird irgendwann (lange bevor sie kaputt gehen) nicht mehr vom Controller verwendet; stattdessen verwendet er an ihrer Stelle eine noch ungenutzte Zelle (entweder aus einem Reservebereich oder aus dem Bereich, der durch (**auto**)**trim** freigegeben wird; Problem: der SSD-Controller weiß nicht, welche Blöcke vom Dateisystem (wie häufig) verwendet werden

**trim** = optimieren eines Flashmediums; die meisten SSDs können das selbst (**autotrim**)

**fstrim**

wird verwendet um nicht mehr verwendete Speicherbereiche auf einer SSD freizugeben; übermittelt an den SSD-Controller eine Liste von Blöcken, die das Dateisystem nicht mehr braucht; der Controller

# FORM FACTOR

# m.2

* Ermöglicht die Anbindung über bis zu **PCIe 3.0 x4**. Damit stünde eine Bandbreite von 32 GBit/s zur Verfügung(mehr als das Fünffache von SATA 3)

* Mit PCIe 2.0 sind pro Lane 5 GBit/s möglich

  Eine Anbindung über zwei Lanes mit PCIe x2 limitiert die Bandbreite auf 10 GBit/s, ist also somit rund 50 Prozent schneller als SATA 3

* Die meisten derzeit erhältlichen M.2-SSDs sind **Key B+M**. Sie besitzen beide Aussparungen und können somit in Key-B- wie auch Key-M-Ports  gesteckt werden.

# INTERFACE

## **AHCI** vs NVMe

* Advanced Host Controller Interface
* Non-Volatile Memory Express

## Kompatibilität

* Achtung insbesondere bei der Anbindung von M.2-SSDs
* Immer mehr moderne PCs und Laptops nutzen die **M.2-Schnittstelle**, um SSDs anzubinden. Hier kommt AHCI ins Spiel, da viele ältere Systeme  lediglich AHCI unterstützen. In diesem Fall sind M.2-SSDs im AHCI-Modus  betriebsbereit.
* Die Unterstützung von AHCI und NVMe hängt auch von der **BIOS-Version** ab, die im System verwendet wird. Ältere Systeme verfügen oft über ein klassisches BIOS, während neuere Systeme das moderne UEFI-BIOS verwenden. UEFI-BIOS bietet in der Regel eine bessere Unterstützung für NVMe-SSDs, während AHCI immer noch eine verlässliche Option für SATA-basierte SSDs ist

## AHCI

* wurde ursprünglich für herkömmliche rotierende Festplatten entwickelt,  konnte aber auch den Übergang zu schnelleren SSDs unterstützen
* wurde für den Betrieb von SSDs optimiert, die auf **NAND**-Flash-Speicher basieren

## NVMe

* **non volatile memory over PCI-express**
* SSD direkt am PCI-Express-Bus - kein Controller; höhere Geschwindigkeit
* nutzt die **PCIe-Schnittstelle**