# LINKS

- https://wiki.debian.org/LVM
- https://tldp.org/HOWTO/LVM-HOWTO/

# LVM

**Logical Volume Manager**; dynamische und hardwareunabhängige Verwaltung von Speicherplatz über mehrere Festplatten; Abstraktion physikalischer Datenträger gegenüber dem Dateisystem; Paket: **lvm2**;

Einsatzzweck: dynamische Zuweisung von Speicherplatz an virtuelle Maschinen;

LVM bietet die Möglichkeit, mehrere Partitionen (**Physcial Volumes, PV**) logisch zusammenzufassen (**Volume Group, VG**) und in diesem logischen Verbund neue "logische Partitionen" (**Logical Volume, LV**) anzulegen; der Volume Group können dynamisch weitere Geräte hinzugefügt werden, wodurch sich der verfügbare Speicherplatz erhöht.

**GParted** kann mit LVM Partitionen umgehen.

## Vorteile & Features

- Skalierbarkeit: Im LVM können alle Elemente (PV, VG, LV), außer der PE, im laufenden Betrieb vergrößert und verkleinert werden. Allerdings muss auch das darin liegende Dateisystem an die neue Größe angepasst werden können. Nicht jedes Dateisystem beherrscht das Vergrößern oder Verkleinern.
- Probleme durch falsche Partitionierung können vermieden werden
- Die Kapazität mehrerer Festplatten kann zu einem Volume zusammengefasst werden
- Verschiedene Versionen von Daten sind durch sogenannte "Snapshots" möglich; die Datensicherung wird durch diese "Snapshots" vereinfacht
- LVM hat einen hohen Datendurchsatz und beeinträchtigt das System nur unwesentlich

## Nachteile

- Liegen die LVs bzw. PEs auf mehreren Festplatten verteilt, so wird das Risiko des "Single-Point-of-Failure" größer, da beim Ausfall einer Platte alle Daten verloren sind, ähnlich wie bei RAID 0; werden 4 Platten in einem einzigen LV zusammengefasst, ist das Ausfallrisiko 4x höher
- Unübersichtlichkeit: Wo liegen meine Daten (physikalisch)?
- Zugriff auf LVM-Partitionen ist nur mit Linux möglich
- Um auf Logical Volumes zugreifen zu können, benötigen sog. Rescue CDs auch LVM-Unterstützung

# CONFIG

```sh
sudo nano /etc/lvm/lvm.conf # die einzige Konfigurationsdatei
```


# PATHS

```sh
realpath /dev/os/debian 
# /dev/dm-2
realpath /dev/mapper/os-debian 
# /dev/dm-2
```
# HOW TO

## make snapshot

```sh
sudo lvextend --resizefs -L 120GB /dev/mapper/os-debian # resize
sudo lvcreate --snapshot --size 40GB --name os-debian-snap /dev/mapper/os-debian
```

## separate /home afterwards

```sh
sudo du -sh /home # check size of current home
sudo lvcreate -L 40G -n debian-home os
sudo mkfs.ext4 /dev/os/debian-home
sudo mkdir /mnt/debian-home
sudo mount /dev/os/debian-home /mnt/debian-home
sudo chown frida: /mnt/debian-home
sudo rsync -av /home/ /mnt/debian-home # copy
sudo umount /mnt/debian-home # unmount

# eventually increase size if contents don't fit:
sudo lvextend --resizefs -L +20GB /dev/os/debian-home
```

### modify fstab

```sh
sudo nano /etc/fstab

/dev/os/debian       /      ext4  errors=remount-ro 0  1
# add this line:
/dev/os/debian-home  /home  ext4  defaults  0  2
```

### boot into live image

```sh
# make sure new /home is mounted
# delete old /home 
sudo rm -r /media/mint/<mountpoint>/home/*
# shrink system lv where /home formerly was located

sudo umount /media/mint/<mountpoint>
sudo e2fsck -f /dev/os/debian
sudo lvreduce -L 60GB --resizefs /dev/os/debian
```

# SNAPSHOTS & BACKUPS

- Lots of change = not very space efficient
- Small amount of change = very space efficient
- We only need enough space to store the changes that we make to the source

## create

- `--extents 20%ORIGIN` size should depend upon the amount of writes that are expected; often 20% of the origin LV is enough.

```sh
sudo lvcreate --snapshot --size <size> --name lvsnap /dev/<vg>/<lv> 
sudo lvcreate --snapshot --size 40GB --name os-debian-snap /dev/os/debian

sudo lvcreate --snapshot --extents 20%ORIGIN --name os-debian-snap-$(date +%y-%m-%d) /dev/os/debian
# For a snapshot, the size can be expressed as a percentage of the total size of the origin LV with the suffix %ORIGIN

sudo lvcreate --snapshot --extents 20%ORIGIN --name os-debian-snap-2 /dev/os/debian
```
 
 Finden Schreibvorgänge auf das originale _lvolume_ statt, so werden die betroffenen Blöcke vorher physisch auf _lvsnap_ kopiert, erst danach werden die Änderungen auf _lvolume_ durchgeführt. Damit werden Inkonsistenzen vermieden, die sonst bei Backups von Datenträgern im laufenden Betrieb z.B. durch offene Dateien, Schreibvorgänge, etc. entstehen. 
 
 - Der Snapshot ist so lange gültig, wie sich das Original um nicht mehr als den mit `--size` angegebenen Wert verändert. 
 - Die **Schreibrate** auf _lvolume_ wird verringert, solange ein Snapshot davon existiert.

## mount

```sh
sudo mkdir /mnt/debian-snap
sudo mount /dev/os/debian-snap-1 /mnt/debian-snap-1
cd /mnt/debian-snap
sudo umount /mnt/debian-snap
```
## merge & restore

führt das originale LV und den Snapshot zusammen; ggf. erfolgt die Zusammenführung erst beim Neustarten des Computers (z.B. bei root). Nachdem der Vorgang erfolgreich abgeschlossen wurde, wird der Snapshot gelöscht.

- Before merging the snapshot make sure that the origin source is in **unmounted state** or else the merging will be postponed for a pending activation of the logical volume.

```sh
lvconvert --merge /dev/system/snap_data
# Can't merge until origin volume is closed.
# Merging of snapshot system/snap_data will occur on next activation of system/data.
```

**lvm reactivation**
If for some reason you cannot perform this re-activation, then this snapshot merge will happen during the next reboot

```sh
# 1. Unmount the data partition
umount /data/
# 2. De-activate the data partition
lvchange -an  /dev/system/data
# 3. Re-activate the data partition
lvchange -ay  /dev/system/data
```

## backup

```sh
tar -pzcf backup_lvolume.tar.gz /dev/<vg>/lvsnap 
# backup der Daten des Snapshots

lvremove /dev/<vg>/lvsnap
# entfernt den Snapshot wieder, da ansonsten die Verfolgung der Änderungen weiterlaufen würde, was die Performance verschlechtert
```

## extend size

automatically extending the snapshot volume

```sh
# Example
# Using 70% autoextend threshold and 20% autoextend size, when a 1G
# snapshot exceeds 700M, it is extended to 1.2G, and when it exceeds
# 840M, it is extended to 1.44G:

snapshot_autoextend_threshold = 70
snapshot_autoextend_percent = 20
```

## add to grub

```sh
sudo nano /etc/grub.d/40_custom

menuentry "My LVM Snapshot" {
    set root=(lvm/os-debian-snap-1)
    linux /vmlinuz root=/dev/os/debian-snap-1 ro
    initrd /initrd.img
}
```

# PE

**Physical Extend**: die kleinste mögliche Speichergröße in einem LVM; vergleichbar mit den Sektoren von normalen Festplatten bzw. Partitionen. Es ist nicht möglich, die Größe der PEs nachträglich zu verändern. Dies ist aber auch nicht nötig, da die Anzahl der PEs seit LVM2 unbegrenzt ist, die Standardgröße beträgt 4 MByte.

# PV

**Physical Volumes:** der eigentliche Datenspeicher eines LVMs; kann eine Partition oder eine ganze Festplatte sein (oder ein loop device oder meta device);

**vorbereiten**

mit fdisk eine Partitionstabelle vom Typ **8e** anlegen

## pvcreate

pvcreate initializes physical volume(s) for use by LVM
allows the PV to be used in a VG

```sh

```

### pvmove

**verschieben**

```sh
pvmove <pvolume> # moves the content of _pvolume_ to (any) other PVs in the same VG.

pvmove <pvolume1> <pvolume2> # moves the content of _pvolume1_ to _pvolume2_.

-A 
--autobackup y|n # specifies if metadata should be backed up automatically after a change; enabling this is strongly advised!

-n
--name <name> # moves only the extents belonging to the named LV.

-v
--verbose # sets verbose level; repeat from 1 to 4 times to increase the detail of messages
```

### pvs / pvdisplay

- **pvs** uses a more compact and configurable output
- **pvdisplay** shows more information

# VG

**Volume Groups**: Container für ein oder mehrere Physical Volumes

```sh
vgscan
```

- sucht (in allen angeschlossenen Blockgeräten) nach VGs und PVs
- das Ergebnis wird in /etc/lvm/cache/.cache gespeichert

### vgcreate

```sh
sudo vgcreate <vgroup> <pvolume1> <pvolume2> 
# erstellt eine VG mit dem Namen <vgroup>
```

### vgextend

```sh
vgextend <vgroup> <pvolume> # <pvolume> wird <vgroup> hinzugefügt
```

### vgreduce

**verkleinern:** vgreduce -**a** _vgroup_ entfernt alle leeren PVs aus _vgroup_

1. modprobe dm-mirror lädt den _mirror_-Treiber welcher für pvmove notwendig ist
    
2. pvmove _pvolume_ verschiebt den Inhalt (die PEs) des PVs auf die anderen PVs der VG; dies ist nötig, um z.B. das PV aus einer VG zu entfernen; um ein PV mit pvmove zu leeren, muss genügend Platz auf den restlichen PVs vorhanden sein!
    
3. vgreduce _vgroup_ _pvolume_ entfernt _pvolume_ aus _vgroup_
    
## vgremove

**löschen:** vgremove _vgroup_

## vgs / vgdisplay

**Information**

vgs less information, more compact

vgdisplay more information, less compact

## vgcfgbackup

- backup volume group configurations (metadata NOT the data content of LVs)
- if no VGs are named, back up files are created for all VGs
- default: each VG is backed up into a separate file bearing the name of the VG under `/etc/lvm/backup/`

## vgsplit

- moves PVs into a new or existing VG
- if the destination VG does not exist, a new VG is created
- can only move complete PVs (see pvmove for moving part of a PV)

## vgmerge

- merges two existing VGs
- The inactive source VG is merged into the destination VG if physical extent sizes are equal and PV and LV summaries of both VGs fit into the destination VG's limits.

# LV

- **Logical Volumes**: eine Art Partition innerhalb einer Volume Group
- Es ist nicht möglich Logical Volumes außerhalb einer VG zu erstellen.
- Das LV kann wie eine normale Partition verwendet werden, d.h. es können Dateisysteme darauf angelegt werden und es kann gemounted werden

ein LV kann über den Pfad **/dev/**_**vgroup**_**/**_**lvolume**_ oder über _**vgroup lvolume**_ angesprochen werden

## lvcreate

```sh
sudo lvcreate -n <name> -L 2G <vgroup> # erstellt ein LV der Größe 2 Gigabyte mit dem Namen <lvolume> in der VG <vgroup>

-n
--name <lv_name> # specifies the name of a new LV.
-L 
--size [+]<size> 
--size [-]<size> # specifies the size of the new LV in absolute or relative fashion.

mkfs.ext4 /dev/<vg>/<lv> # erstellt auf dem LV ein Dateisystem
```

### RAID

Wegen der höheren Fehleranfälligkeit sollte das LVM in einem RAID-Verbund installiert werden, der Redundanz bietet (mindestens RAID-Level 1)

```sh
lvcreate --type linear|striped|mirror|raid # LV type, 
# also known as: segment type or segtype
# = Methode mit der die Daten auf den Physical Volumes verteilt werden
```

- **linear**
  Die Logical Extents werden hierbei linear aufsteigend mit den Physical Extents verknüpft; lineare LVs sind nachträglich skalierbar
- **striped**
  benachbarte LEs sind mit PEs aus zwei verschiedenen PVs verknüpft; wird gleichzeitig auf Daten benachbarter LEs zugegriffen, kommen die Daten von zwei verschiedenen Laufwerken, was die Datentransferrate im Einzelfall erhöhen kann; striped LVs sind nachträglich nicht vergrößerbar!

#### LVM + RAID 0

LVM unterstützt _striping_, wodurch ein Performance verbessert wird (solange die stripes auch tatsächlich auf verschiedene Platten verteilt werden)

```sh
lvcreate -i
lvcreate --stripes <number> # specifies the number of stripes in a striped LV (= number of PVs that a striped LV is spread across)

lvcreate -I
lvcreate --stripesize <size> # amount of data that is written to one device before moving to the next in a striped LV.
```

#### LVM + RAID 1

```sh
lvcreate -m
lvcreate --mirrors <number> [<targetpv>] 
# specifies the number of mirror images in addition to the original LV image; 
# a targetPV can be given to specify the device the image should be placed on.
```

#### LVM + RAID 10

```sh
lvcreate --type raid10 -m mirrors -i stripes -L <lv_size> -n <lv_name> <vol_grp>
```

## lvextend

```sh
lvextend --resizefs -l +100%FREE /dev/mapper/vm208-root
sudo lvextend --resizefs -L 120GB /dev/mapper/os-debian

lvextend --resizefs -L 5G /dev/mapper/os-debian # vergrößert das LV und das Dateisystem auf 5 GB
```

```sh
-r
--resizefs # resizes the underlying filesystem together with the LV using fsadm
-l
--extents [+]Number[PERCENT] # Specifies  the  new  size  of the LV in logical extents.  The --size and --extents options are alternate methods of specifying size.
-L
--size [+]Size[m|UNIT] # Specifies the new size of the LV.  The --size and --extents options are alternate methods of specifying size.
```

## lvreduce

```sh
# 1.
umount <lv> # Dateisystem aushängen
# 2.
fsck -t <type> <lv> # Dateisystem prüfen
# 3.
resize2fs <lv> <size> # Dateisystem verkleinern
# 4.
lvreduce # LV verkleinern
```

## lvremove

```sh
lvremove /dev/<vg>/<lv>
```

## lvresize

resize a logical volume

## lvrename

```sh
lvrename dev/<vg>/<lv> <newname>
sudo lvrename /dev/os/os-debian-snap /dev/os/debian-snap
```

## lvs / lvdisplay

```sh
lvs # less information, more compact
lvdisplay [vgroup] # more information, less compact

sudo lvdisplay /dev/os/debian-snap
sudo lvs /dev/os/os-debian-snap-25-04-14

# Data% # viel Kapazität der Snapshot aktuell verbraucht
```

## output control

```bash
--noheadings
-v
--verbose # repeat from 1 to 4 times to increase the detail of messages
```

```bash
-o
--options option1 option2 # Comma-separated, ordered list of fields
# The prefix + will append the specified fields to the default fields
sudo lvs -o help # view the list of all available fields
```

```bash
sudo lvs -o lv_all # view  all  LV  fields
sudo lvs -o lv_name # debian-snap-1
sudo lvs -o origin --noheadings /dev/os/debian-snap-1 # debian
```

## attributes

```sh
LV                     VG Attr
debian                 os Owi-aos---
# type: origin with merging snapshot
# permissions: writeable
# allocation: inherited
# -
# state: active
# device open
# target type: snapshot
```

1. **Volume type:** 
    - m --> mirrored
    - M --> ,irrored without initial sync
    - o --> origin
    - O --> origin with merging snapshot
    - (r)aid, (R)aid without initial sync, (s)napshot, merging (S)napshot, (p)vmove, (v)irtual, mirror or raid (i)mage, mirror or raid (I)mage out-of-sync, _mirror (l)_og device, under (c)onversion, thin (V)olume, (t)hin pool, (T)hin pool data, raid or thin pool **m**(e)tadata
2. **Permissions:**
    - w --> writeable
    - r --> read-only
    - R --> read-only activation of non-read-only volume
3. **Allocation policy:**
    - a --> anywhere
    - c --> contiguous
    - i --> inherited
4. **fixed (m)inor**
5. **State:** 
    - a --> active
    - s --> suspended
    - I --> invalid snapshot
    - S --> invalid suspended snapshot
    - snapshot (m)erge failed, suspended snapshot (M)erge failed, mapped (d)evice present without tables, mapped device present with (i)nactive table
6. **device (o)pen**
7. **Target type:** 
    - m --> mirror
    - r --> raid
    - s --> snapshot
    - (t)hin, (u)nknown, (v)irtual. This groups logical volumes related to the same kernel target together. So, for example, mirror images, mirror logs as well as mirrors themselves appear as (m) if they use the original device-mapper mirror kernel driver; whereas the raid equivalents using the md raid kernel driver all appear as (r). Snapshots using the original device-mapper driver appear as (s); whereas snapshots of thin volumes using the new thin provisioning driver appear as (t).
8. Newly-allocated data blocks are overwritten with blocks of **(z)eroes** before use.
9. **(p)artial**: One or more of the Physical Volumes this Logical Volume uses is missing from the system.

```sh
LV                     VG Attr
debian-home            os -wi-ao----
debian-snap-1744665605 os swi-a-s---
```