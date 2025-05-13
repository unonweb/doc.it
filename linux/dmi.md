DMI (BIOS)
--------------------------------------------------

*Desktop Management Interface*; eine vom BIOS zur Verfügung gestellte Tabelle, aus welcher Informationen über das BIOS und das System in einer standardisierten Art und Weise ausgelesen werden können.

### dmidecode

DMI table decoder; dumping a computer's DMI (some say SMBIOS) table contents in a human-readable format. This table contains a description of the system's hardware components, as well as other useful pieces of information such as serial numbers and BIOS revision.

```sh
dmidecode --type <type>
dmidecode -t <type> # displays only the entries of type (bios; system; baseboard; chassis; processor; memory; cache; connector; slot)   

dmidecode -t bios|system|baseboard|chassis|processor|memory|cache|connector|slot
```
