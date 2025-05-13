# strategy

### 3-2-1 Regel

- **3** Instanzen 
- an **2** Standorten 
- von **1** Datensatz 
- **original + indoor copy + outdoor copy**

# mechanism

## snapshot

1. Die festzuhaltende Partition wird auf **read-only** geschaltet (**freeze**); dabei muss darauf geachtet werden, dass das Dateisystem zu diesem Zeitpunkt in einem konsistenten Zustand ist; danach werden alle Änderungen umgelenkt und in eine (temporäre) Snapshot-Datei geschrieben; gelesen wird währenddessen aus der eingefrorenen Partition und aus der Snapshot-Datei (je nachdem, ob es sich um eingefrorene Information oder schon Teil der Änderungen handelt); geschrieben wird in die Snapshot-Datei; das System ist währenddessen verlangsamt
   - von der eingefrorene Datei/Partition kann dann ein **backup** gemacht werden
   - es können experimentelle Änderungen durchgeführt und der Snapshot bzw. die eingefrorene Partition als **Wiederherstellungspunkt** verwendet werden
2. der Snapshot wird aufgelöst indem die eingefrorene Partition wieder auf **read/write** geschaltet wird und dann alle Änderungen aus der Snapshot-Datei dorthin verschoben werden.

# cycle

* 1x/Monat: vollständiges Backup
* 1x/Woche: differentielles Backup
* 1x/Tag: inkrementelles Backup

## full backup

- sollte (immer) am Wochenende ausgeführt werden

![[full_backup.jpg]]

## differential backup

- beinhaltet alle Daten, die sich seit dem letzten vollständigen Backup geändert haben
- **Nachteil**: 
    - Größe: da jeden Tag sämtliche Änderungen seit dem letzten vollständigen Backup gesichert werden, wächst die Größe des differentiellen Backups im Lauf der Woche stark an  
- **Vorteil**: 
    - für eine Wiederherstellung sind max. zwei Datensätze erforderlich: 
        - das vollständige 
        - das letzte inkrementelle Backup 
          (welches die volle Differenz zum vollständigen Backup enthält)

![[diff_backup.jpg]]

## incremental backup

- beinhaltet alle Daten, die sich seit dem letzten vollständigen oder dem letzten inkrementellen Backup geändert haben
- **Vorteil**: da jeden Tag nur die Änderungen zum vorherigen Tag gesichert werden, wächst die Größe des inkrementellen Backups kaum an
- **Nachteil**: für eine Wiederherstellung sind alle inkrementellen Backups seit dem letzten vollständigen Backup erforderlich; sollte eines der inkrementellen Backups korrupt sein, sind alle nachfolgenden unbrauchbar

![[incr_backup.jpg]]