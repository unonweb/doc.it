---
title: Computer Hardware
---

# Zusammenbau eines Computers

#### Empfohlene Reihenfolge

1. RAM, CPU, Lüfter auf das Mainboard installieren
2. Mainboard in da Gehäuse einbauen
3. SSD, HDD, DVD anschließen
4. ATX Spannungversorgung Mainboard und CPU anschließen
5. restliche Bauteile

#### Worauf ist zu achten um Schäden zu vermeiden?

* ATX-Stromversorgung muss gesteckt sein
* CPU-Lüfter muss angeschlossen sein
* Das Netzteil muss ausgeschaltet sein


Motherboard
================

## Auswahl eines Motherboards

1. Formfaktor
2. CPU-Sockel: bestimmt über die CPU
    * Kontaktstellen: 1151
      * Skylake
3. Chipsatz: bestimmt über die Mainboard-Leistung
4. Anschlüsse

## Formfaktor (ATX)

ATX-Format (*Advanced Technology Extended*) ist eine Normung für Gehäuse, Netzteile, Hauptplatinen und Steckkarten von Mikrocomputern. Trotz des Versuchs, das *BTX-Format* als Standard durchzusetzen, ist das ATX-Format das dominierende Format bei PCs

* *ATX*: 305 mm × 244 mm
* *Micro-ATX*: 244 mm × 244 mm.


#### ATX Power supply
* *ATX 24 pin main power supply connector* is the standard motherboard power connector in computers today; supplies power into an ATX-type computer motherboard. This in turn distributes power to internal components, such as the CPU, memory module, hard disk and graphics card.
  * the original cable had 20 pins 
* *ATX12V Connector*: CPUs used to be powered by the 20 pin main power cable. Almost all current motherboards power their CPU with a 12 volt CPU power cable. There are two kinds: 
  * the *4 pin 12V* cable (often called a *P4*) 
  * the *8 pin 12V cable* (called an *EPS12V*)


## System/Front panel connector

* Alternatively referred to as the *f-panel* or *front panel connector*, *system panel header* 
* mainly consists of *power button*, *reset button*, and *LEDs* (power, hard drive activity)


Types of system panel cables:
* *HDD LED* (IDE LED)   # The LED activity light for the hard drive. This indicator is the light that flashes as information is being written to and read from the hard drive.
* *PLED* (Power LED)    # The LED power light, which indicates when the computer is on, off, or in Standby.
* *PWRSW* (Power SW)    # Controls the power button that allows you to turn on and off the computer.
* *Reset SW*            # Handles the reset button to restart the computer.
* *Speaker*             # The internal speaker used to sound the beep noises you hear from your computer when it is booting.

## TPM

TPM (*Trusted Platform Module*), a microchip attached to the motherboard that provides hardware-based cybersecurity.  
You can add a TPM to your PC if it doesn’t come with one, but you’ll need a motherboard that has a *TPM header* to do so.

## COM
(*communication port*); serial port interface
* Anschluss eines *Serial Port RS232*
* can refer not only to physical ports, but also to emulated ports, such as ports created by Bluetooth or USB adapters. 

## Chipset / Chipsatz

* Im Allgemeinen: mehrere zusammengehörende integrierte Schaltkreise, die zusammen eine bestimmte Aufgabe erfüllen
* Im Speziellen (Motherboard): unterstützt den Mikroprozessor bei seiner Aufgabe. Grund für die Aufteilung auf mehrere Schaltkreise ist hierbei die Anzahl der benötigten elektrischen Anschlüsse. 
* controls communications between the processor and external devices
* plays a crucial role in determining system performance.
* a set of electronic components in an *integrated circuit* known as a "*Data Flow Management System*" that manages the data flow between the processor, memory and peripherals.

## SATA

*Serial ATA* (Serial AT Attachment);

* V1
  * release 2000
  * replaces PATA
* V2
* V3

### eSATA
*External SATA*

### mSATA
*Mini-SATA*
* release: 2009

## CMOS

Ein CMOS-Speicher (*Complementary Metal Oxid Semiconductor*) ist ein statischer RAM-Baustein (RAM = Random Access Memory) mit vielen Halbleitern auf kleinem Raum und geringem Stromverbrauch.

## M.2

* replaces the *mSATA* standard
* supports NVM Express (*NVMe*) as the logical device interface for M.2 PCI Express SSDs

bus interfaces provided through the M.2 connector are *PCI Express 4.0* (up to four lanes), *Serial ATA 3.0*, and *USB 3.0*.  
It is up to the manufacturer of the M.2 host or module to select which interfaces are to be supported, depending on the desired level of host support and device type.

Busse
-----------

 Quick-Path-Schnittstelle (QPI), die eine Punkt-zu-Punkt-Verbindung zwischen der CPU und dem integrierten Speichercontroller herstellt.

* Die Taktfrequenz und die Bus-Breite (in Bit) bestimmen die Geschwindigkeit, mit der die Daten zwischen Prozessor (CPU) und Chipsatz übertragen werden.


### Front Side Bus (FSB)

* überträgt Daten zwischen der CPU und dem *Memory-Controller-Hub*
* Schnittstelle bzw. eine Direktverbindung zwischen Prozessor und *Chipsatz (Northbridge)*
* gibt den Takt aller angesprochener System-Komponenten vor, der aber mit Multiplikatoren und Teilern verändert werden kann.
  * Beispiel: Prozessortakt = FSB × CPU-Multiplikator


* Der Front-Side-Bus entscheidet über die Bandbreite der CPU zum Chipsatz, zum Arbeitsspeicher, zur Grafikkarte und zum Rest des Systems. 
* Die Bandbreite der CPU sollte im Idealfall gleich der Bandbreite des Hauptspeichers sein. Ansonsten sind Prozessor und Speicher schlecht aufeinander abgestimmt und die Leistung der einen oder anderen Komponente wird schlicht verschwendet.

Die CPU und der Arbeitsspeicher müssen idealerweise mindestens die FSB-Geschwindigkeit des Mainboards erreichen. Dann läuft das System mit der schnellstmöglichen Geschwindigkeit.  

Früher galt die Devise, dass der Hauptspeicher und der Front Side Bus über die selbe Taktfrequenz verfügen sollten, auch wenn der Chipsatz eine schnellere Speichergeschwindigkeit unterstützte, er sich an der Geschwindigkeit des FSB orientierte. Heute aber ist diese Maxime überholt: Chipsatz und FSB müssen nicht über die selbe Taktfrequenz verfügen, sondern so hoch wie möglich takten.  

### Direct-Media-Interface (DMI)

* stellt das eine Punkt-zu-Punkt-Verbindung zwischen einem integrierten Intel *Speichercontroller* und einem Intel *I/O-Controller-Hub* auf dem Mainboard des Computers her

Terminology
-------------------

### pin header
*Stiftleiste* ist ein Steckverbinder mit mehreren in Reihe angeordneten Stiftkontakten, der auf Leiterplatten in der Elektronik Verwendung findet.