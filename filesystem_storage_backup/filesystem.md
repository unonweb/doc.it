# GUIDE

- Cross-device compatibility? **exFAT**
- Cross-device compatibility on old devices? **FAT32** and lot of zip files.
- Apple ecosystem on Flash NAND? **APFS**
- Apple ecosystem on mechanical drive? **HFS+**
- Windows ecosystem? **NTFS**
- Linux ecosystem? **Ext4**
- Linux for low powered devices? **F2FS** may be an option, **exFAT**
- Servers? **ZFS**, **BTRFS**, **XFS**, review your options…

# TECH

## terms

### filesystem

Regal mit sehr vielen Kisten. In jeder davon befinden sich weitere Kisten, die ihrerseits wiederum kleinere Kisten enthalten können. Die kleinsten Kisten sind so groß wie die Blockgröße des Dateisystems, in den meisten Fällen also 4 KByte. Steht man nun vor dem Regal und hat die Aufgabe, etwas darin zu verstauen, muss man sich entscheiden, in welche Kiste man es packt. Eine Strategie besteht darin, eine Kiste so gut wie möglich voll zu packen, dann die nächste zu füllen und so weiter. Eine andere Herangehensweise wäre, so wenig Kisten wie möglich öffnen zu müssen. Um die verstauten Gegenstände so schnell wie möglich wiederzufinden, werden die Kisten beschriftet (**Metadaten**).

- **Block** (phys): früher 512 Byte; heute 4 Kilobyte Standard-Blockgröße;
- **Block** (FS): Größe kann bei der Formatierung angegeben werden; heute i.d.R. 4K (abwärtskompatibel zu 512 Bytes physikalischer Blockgröße)
- **Inode**: innerhalb eines Dateisystems fest angelegt
- **Datei**: Hardlink zu einem Inode

Bei einer Blockgröße von 4 Kb belegt eine 1Kb große Datei 4 Kb. Bei vielen Dateien unter 4 Kb kann es zur Verknappung der Inodes kommen bevor der Festplattenplatz knapp wird

### inode

**Index Node**; Teil des Dateisystems, der die **Metadaten** über eine Datei speichert:
- Größe
- UID
- GID
- Zugriffsrechte
- Datum

Jeder Inode hat eine eindeutige Nummer (`ls -i <file>`); inodes werden zur Adressierung und Verwaltung vom Kernel verwendet
### extents

fassen mehrere Speicherblocks zu einem zusammenhängenden Bereich (bis zu 128 MByte) zusammen, was Fragmentierung reduziert und die Leistung beim Umgang mit großen Dateien erhöht.

### superblock

- **Inhalt**: Gesamtgröße des Dateisystems (Blöcken/Inodes); Anzahl der freien Blöcke; Anzahl der Blöcke für die Inodes; Anzahl der freien Inodes; Adresse des ersten Datenblocks; Größe eines Datenblocks; Zeitpunkt des mounts; Zeit der letzten Änderung; mount counts; Maximaler mount count vor fsck; Status des Dateisystems: sauber oder mit Fehlern; Verhalten bei Fehlererkennung; Zeitpunkt der letzten Prüfung; Maximaler Zeitraum zwischen zwei Prüfungen
- **Arbeitsspeicher**: Der S. wird beim Mounten des Dateisystems in den Arbeitsspeicher geladen; alle Veränderungen des Superblocks werden dort vorgenommen. Die eigentlichen Veränderungen an den Dateien im Dateisystem werden aber tatsächlich minütlich auf der Platte vorgenommen. Erst beim unmount (i.d.R. shutdown) wird der im Speicher liegende Superblock physikalisch auf die Platte geschrieben.
- **Risikio der Inkonsistenz** bei nicht korrektem abhängen: Superblock beschreibt nicht mehr die tatsächliche aktuelle Situation des Dateisystems.
- **Lösung 1**: **fsck** beim Booten
- **Lösung 2: journal**: jede Transaktion zwischen System und Platte wird hier mitprotokolliert, so daß nach einem Absturz in sehr kurzer Zeit wieder ein konsistenter Zustand hergestellt werden kann. Das bedeutet nicht, daß alle Daten wiederhergestellt werden können, die durch den Absturz womöglich verlorengingen, sondern nur, daß es zu keiner Diskrepanz zwischen Superblock und Dateisystem kommen kann.

### copy-on-write

- ZFS, Btrfs

Beim Schreibzugriff wird nicht der Inhalt der Originaldatei geändert, sondern der veränderte Inhalt als neue Datei an eine freie Stelle geschrieben; im Anschluss werden die Metadaten auf die neue Datei angepasst; bis die Metadaten angepasst sind, bleibt die Originaldatei erhalten und kann nach einem Absturz weiter verwendet werden (+ Datensicherheit) (+ verzögerungsfreie Erstellung von Snapshots)

**+** Performance: Je mehr Blöcke einer Datei zusammenhängen, desto schneller arbeitet das Dateisystem, da der Lesekopf die Daten am Stück einlesen kann. Aus diesem Grund legt Btrfs von geänderten Dateien stets eine Kopie an und stellt damit sicher, dass die Blöcke zusammenhängen

**-** Defragmentierung

### read-modify-write

Datenbloecke werden in einer sog. atomaren Operation eingelesen, veraendert und anschliessend überschrieben. Traditionelle Dateisysteme überschreiben beim Speichern einer Datei die vorhandenen Datenblöcke. Ändert sich dabei die Größe einer Datei, benutzt das Dateisystem freie Blöcke in der unmittelbaren Nähe.

- Je nachdem, wie viel Zeit zwischen dem ersten Speichern und dem Überschreiben verging, kann diese "unmittelbare Nähe" schon ganz weit entfernt sein – der Lesekopf muss somit größere Distanzen zurücklegen.
- Sollten die Daten beim Schreibvorgang korrumpiert worden sein, gibt es kein Zurueck, da die originalen Daten ueberschrieben wurden.

### journal

im journal wird nicht protokolliert was im Dateisystem geändert wird, sondern nur wo die Änderungen durchgeführt wurden und ob sie erfolgreich durchgeführt wurden; im Fall eines nicht korrekt ausgehängten Dateisystems können mithilfe des journals die Stelle lokalisiert werden, wo zuletzt Änderungen durchgeführt wurden und die überprüft werden müssen. Ein journal hat also (lediglich) den Zweck, im Fall das Falles nicht mehr das ganze Dateisystem überprüfen zu müssen; ein journal auf einem Flash-Medium erhöht den Verschleiß;

### Deduplizierung

bedeutet, dass das Filesystem Blöcke die bereits auf den Datenträger geschrieben wurden, nicht erneut schreibt, sondern darauf referenziert.

- wird ein Ordner kopiert und mehrfach gepastet, benötigt dieser nur den Speicherplatz eines Ordners.
- beim wiederholten Backup (auch bei Verwendung verschiedener Ordner/Namen) sorgt die Deduplizerung sorgt dafür, dass nur geänderte Daten zusätzlichen Speicherplatz benötigen.

Die meisten Filesysteme können diesen Vorgang im Nachhinein per geplantem Task erledigen, einige wenige Dateisysteme erledigen die Deduplizerung während des Schreibvorganges, also Inline (**Inline Deduplizierung**). Diese Variante benötigt für jeden Kopiervorgang relativ viel Systemresourcen, also RAM und CPU Performance

### sparse file

… reason why a _**file's size**_ and the _**amount of disk space**_ it takes up may differ.

Unix filesystems support a crude form of compression called **sparse files**: if a block in a file consists entirely of null bytes, it doesn't need to be stored at all; the filesystem puts a special marker instead of a block number in the list of blocks that store the file's content. This compression method isn't systematic: if a program writes a bunch of null bytes, they'll be stored. However, Unix also allows a program to write past the end of a file. In that case, the file is expanded with null bytes, but if those bytes make up a whole block or more, such whole null blocks are not stored.

When you write dd seek=…, the dd program seeks to the given position before it starts writing. In your case, taking app as an example, the position seems to have been about 62MB past the end of the file, so there are about 62MB worth of null bytes that are stored implicitly, in nonexistent blocks. This storage detail is not exposed to applications (unless they use non-portable interfaces to find out, which very few do), so when the signing program reads its input, all it knows is that there are about 63MB of data, and so it writes 63MB, of which about 62MB are null bytes, to its output file.

### hardlinks

Zwei Dateien sind miteinander fest (hart) verlinkt: Wenn nun eine geändert wird, ändert sich auch die andere (weil es in Wirklichkeit nur eine einzige Datei gibt). Beide Dateien sind nur ein Verweis auf die wirkliche Datei. Wird eine gelöscht, reduziert sich nur die Anzahl der Links.

- können nicht auf Verzeichnisse angewendet werden
- können nicht über die Grenzen von Dateisystemen hinweg erstellt werden
- wird einer von zwei Hardlinks auf eine Datei gelöscht, bleibt der andere immer noch funktionsfähig
- speichern den Inode, belegen keinen Speicher und sind schneller

```sh
ls -i # gibt zusätzl. den Inode der Datei an - ist dieser bei zwei Dateien identisch, handelt es sich um Hardlinks.
```

#### Hardlinks & Windows

Der Windows Explorer kann den Unterschied zwischen Hardlinks und tatsächlichen Dateien nicht erkennen. Der Speicher wird berechnet, als wären Hardlinks die wirklichen Dateien.
## blocks & inodes

**Blocks** are the most granular addressable storage unit. Every file on your PC is stored across one or more blocks. A block is typically 4096 bytes in size. This depends on the hardware you have and the software (i.e. the filesystem) on top of it.

Filesystems allow us to find the contents of our files from the vast amount of available storage blocks. This is done via so-called **inodes**. An inode contains information about a file in a specially formatted storage block: 

- the file’s size
- where to find the storage blocks that make up the file contents
- its access rules (i.e. who can read, write or execute the file)
- and much more


![[inode_file.png]]


# PARTITIONING

## advantages for multiple partitions

* Multiple File Systems
* Data Protection
* on HDDs reduces the time the magnetic head takes to find data from any particular partition. Organizing the drive data affects how quickly you can access it. 

# FS FEATURES

## journaling

One of the larger sources of reads and writes to any disk is due to a file system’s journal. Certain file systems, such as NTFS, Apple File System, and EXT4  reserve a portion of a disk to store metadata for any given file. This information can include a file’s creation time, recently accessed time, etc. Such data can prove useful in situations where data recovery and integrity are important. When a journal is present, this not only (marginally) reduces the available disk space, but may increase reads and writes, since with every file access, creation, and deletion, the action is  logged.

# OTHER

## iso

eine ISO-Datei enhält ein identisches Abbild des Dateisystems einer CD/DVD; das Dateisystem wird beim Erzeugen eines ISO-Abbildes unverändert kopiert (1:1-Kopie), dadurch bleiben Berechtigungen und andere Metadaten unverändert erhalten.

* ISO 9660 definiert ein Dateisystem für CDs oder DVDs (wird i.d.R. mit einer **RockRidge** bzw. **Joliet**-Erweiterung verwendet)
* **ISO** 13346 (Universal Disk Format) – zusätzlich für DVD±R/W, DVD-RAM und BD-R/E.

entsteht entweder direkt auf dem (Ziel-)Medium („on-the-fly“) oder in Form einer „ISO-Datei“.

mount image.iso um den Inhalt eines ISO-Images zu entpacken muss man es (auf ein loop-device) MOUNTEN!

## MBR

- max. 4TB bzw. 2TB/Partition

Aufbau einer Festplatte mit MBR

Sektor 0 / MBR

| size  | content               | comment                                                      |
| ----- | --------------------- | ------------------------------------------------------------ |
| 64 B  | MBR-Partitionstabelle | 4 Einträge á 16 byte = 4 primäre Partitionen (sda1, 2, 3, 4) + eine erweiterte Partition |
| 446 B | Bootloader            | 1st Stage; Verweis auf 2nd Stage                             |

Ein Betriebssystem muss beispielsweise folgendes wissen: 

* Wo auf der Metallplatte oder wo auf den Flash-Speichern ist der Anfang des Speicherbereichs? Wo das Ende?
* Wie heißt der Bereich überhaupt? 
* Soll alles ein großer Speicher sein oder sind es mehrere kleine? 
* Wenn das Betriebssystem sogar von dieser Festplatte starten soll: Woher soll der Computer das wissen?


`shutdown.exe /r /o /t 00` # neustart im abgesicherten Modus