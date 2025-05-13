# RAID

**Redundant Array of Independent Discs**; Zusammenschluss mehrerer physikalischen Festplatten zu einem logischen Verbund; storage virtualisation

Schwachstelle: Das Ziel, eine Sicherheit gegenüber Hardwareausfällen, wird durch den Einbau von mehr Hardware erreicht (bei Hardware RAIDS);

**Vorteile**:

- Schutz vor Serviceeinbrüchen bei Ausfall eines Mediums: Speichermedien haben eine höhere Ausfallwahrscheinlichkeit als andere Komponenten (wie z.B. RAID-Controller).
- Performance

### Hardware / Software

- Hardware: RAID-Controller: mit 512 MB/1 GB/2GB Arbeitsspeicher
- Software: soviel Arbeitsspeicher wie freigegeben

# LEVEL

## RAID 0

**Striping: Beschleunigung ohne Redundanz**:

![[raid_0.png]]

### Funktion

Striping: Datenblöcke werden in Chunks/Slices einer wählbaren Größe (2 K, 8 K, 1 M) unterteilt und auf mehrere Platten verteilt:

ACEGI BDFH (2 Platten) oder ADG BEH CFI (3 Platten); jede Datei, die größer ist als ein einzelner Chunk/Slice wird auf mehrere Platten verteilt; die ideale Größe der Chunks/Slices sollte ermittelt werden
### Kapazität

100% Nutzdaten
### Vorteil

Lesegeschwindgikeit*n (bei 2 Platten Faktor 2); Schreibgeschwindigkeit*1/2n (bei 4 Platten Faktor 2)
### Nachteil

für die über mehrere Chunks verteilten Dateien besteht ein doppeltes Ausfallrisiko, da sie schon beim Ausfall von nur einer Platte verloren gehen

## RAID 1: Mirroring

![[raid_1.png]]
### Funktion

mehrere (i.d.R. zwei) Platten werden gespiegelt (Redundanz): ABCDE ABCDE
### Kapazität

50% (nur für kleine Server praktikabel)
### Vorteil

- eine Platte darf ausfallen
- Wiederherstellung schnell & einfach
- Lesegeschwindigkeit ist verdoppelt

### Nachteil

- Schreibgeschwindkeit ist nahezu halbiert, da zwei sich drehende Platten nie synchron sind und auf eine Platte immer länger gewartet werden muss;

## RAID 5: Striping + rotating Parity

**Beschleunigung + Redundanz**

![[raid_5.png]]

### Funktion

- mind. 3 Platten
- anstelle einer vollständigen Spiegelung der Daten werden Paritätsdaten durch logische XOR-Verknüpfungen erstellt
- die Chunks/Slices und ihre Paritätsdaten werden abwechselnd nach einem Rotationsverfahren auf die Platten geschrieben
- Weiterentwicklung von RAID 3 und 4
### Kapazität

67 - 94% (bei 16 Platten) (je mehr Platten im Einsatz sind, desto besser ist das Verhältns);
### Vorteil

- eine Platte darf ausfallen (egal wieviele verwendet werden)
- Lesegeschwindigkeit ist um den Faktor n-1 höher
### Nachteil

- Schreibgeschwindigkeit verringert, da bei jedem Schreibzugriff der Paritäts-Sektor ausgelesen, neu berechnet und wieder gespeichert werden muss (read-modify-write, write penalty)
- bei Hardware RAID federn die Controller-Caches dieses Problem ab
- Initialisierung erforderlich
- Wiederherstellung (rechen- und zeit-)aufwändig

## RAID 6

### Funktion

mind. 4 Platten; ähnelt RAID 5,
### Kapazität

50 - 88% (bei 16 Platten)
### Vorteil

- kann den gleichzeitigen Ausfall zweier Platten tolerieren

## RAID 10: Mirroring + Striping

![[raid_10.png]]
### Funktion

- mind. 4 Platten
- ein RAID 0 über mehrere RAID 1
- der RAID 1 wird zuerst angelegt

### Kapazität

50%
### Vorteil

- hohe Performance wie RAID 0
- kombiniert mit der Datensicherheit von RAID 1
- einfache Wiederherstellung

# JBOD

**JBOD:** Just a Bunch of Discs: ABC DEF GHI; Zusammenschluss mehrerer physikalischen Platten, die dem OS als ein logischer Verbund/ein Dateisystem erscheinen und nacheinander voll geschrieben werden; weder redundant, noch independent, daher kein richtiges RAID

# RAID-Wiederherstellung

- **degraded**: das RAID ist noch funktionstüchtig, jedoch nicht mehr "optimal"; die defekte Platte muss ausgetauscht werden, danach wird ein sogenannter **Rebuild** durchgeführt; die Performance sinkt i.d.R. massiv
- **offline**: es sind zu viele Platten ausgefallen; der RAID-Controller kann keine Daten mehr liefern, es liegt nicht mehr ausreichend Redundanz vor

# **MDADM**

**multiple disk administration**; Der Übersicht wegen, macht es Sinn auf den einzelnen Festplatten eines RAIDs je eine Partition maximaler Größe anzulegen; der Partitionstyp unter Linux ist **fd** (Linux raid autodetect);