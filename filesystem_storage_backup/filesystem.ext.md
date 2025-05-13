
# EXT

## versions

- *Alles ist ein Modul:* d.h. durch das hinzufügen bzw. entfernen von Modulen können die verschiedenen ext-Versionen ineinander konvertiert werden
- **journal**: bei ext2 müsste nach Verbindungabbruch fsck durchgeführt werden; bei ext3 mit journal nicht; ein journal auf einem Flash-Medium erhöht den Verschleiß; im journal wird nicht protokolliert was im Dateisystem geändert wird, sondern nur wo die Änderungen durchgeführt wurden und ob sie erfolgreich durchgeführt wurden; im Fall eines nicht korrekt ausgehängten Dateisystems können mithilfe des journals die Stelle lokalisiert werden, wo zuletzt Änderungen durchgeführt wurden und die überprüft werden müssen. Ein journal hat also (lediglich) den Zweck, im Fall das Falles nicht mehr das ganze Dateisystem überprüfen zu müssen.

## ext3

* ext3 = ext2 + journal**; ein ext3fs kann als ext2fs gemountet werden (dabei wird das journal außen vor gelassen)
* Jahr: 2003; 32 bit große Blocknummern

## ext4

* Jahr: 2008; 48 bit große Blocknummern; unterstützt Partitionen oder Volumes, die bis zu 1 EiB groß sind; **5%** des Speicherplatzes sind für root reserviert.
* ext4 is an an advanced level of the ext3 filesystem which incorporates scalability and reliability enhancements for supporting large filesystem. 