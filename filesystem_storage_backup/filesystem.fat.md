
# FAT

File Allocation Table

## fat32

* originally created for floppy disk storage
* introduced in Windows 95
* remains the most common file system today thanks to its usage in memory cards and flash drives
* guarantees that your drive will work with any computer you plug it into
* can’t write individual files over 4GB

## vfat

* Virtual File Allocation Table
* Verwendung: Flash
* Unterstützung vieler OS
* Beschränkung auf 4GB

## exfat

* Extended File Allocation Table
* introduced in 2006
* Verwendung: Flash, externe Festplatten
* Unterstützung vieler OS
  has read/write compatibility with macOS, whereas NTFS is read-only on a Mac
* max. 64 ZB
* Partitionstyp **0700**

disadvantage

* No journaling: When a change on the exFAT partition is done there is no registry of it

  Any interruption, like an accidental cable trip, power loss or a system hang, will lead data corruption. Just imagine copying a 2GB file just half, and wondering why the rest of the file is a bunch of zeros.

* No TRIM
  This command sent by the OS improves performance on Flash NAND based storage (SSDs) on write and delete operations and makes their life last a little longer.

* 

Da Microsoft die Lizenz nicht freigibt, bieten die offiz. Paketquellen exFAT-Unterstützung nur in der Sektion *universe* an. Daher befindet sich exFAT auch nicht im Linux-Kernel, sondern es läuft via **FUSE**.

Pakete: **exfat-fuse** und **exfat-utils** 
