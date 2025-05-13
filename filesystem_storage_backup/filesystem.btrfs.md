# LINKS

- https://btrfs.readthedocs.io/en/latest/
- https://www.matuck.com/tech/2023/09/03/Debian-12-with-LUKS,-BTRFS,-and-subvolumes.html
- https://itsfoss.com/btrfs/
- Debian 12 with LUKS, BTRFS, and subvolumes:
  https://www.matuck.com/tech/2023/09/03/Debian-12-with-LUKS,-BTRFS,-and-subvolumes.html
- BTRFS Should Be Default
  https://thelinuxcast.org/posts/2024/snapshots-are-the-best/
- btrfs.readthedocs.io
    - Status:
      https://btrfs.readthedocs.io/en/latest/Status.html
- fedoramagazine.org
    - Working with Btrfs – General Concepts
      https://fedoramagazine.org/working-with-btrfs-general-concepts/
    - Working with Btrfs – Subvolumes
      https://fedoramagazine.org/working-with-btrfs-subvolumes/
    - Working with Btrfs – Snapshots
      https://fedoramagazine.org/working-with-btrfs-snapshots/

## kernel support

```sh
lsmod | grep btrfs # or
cat /proc/filesystems | grep btrfs
sudo modprobe btrfs # load module
```

# BTRFS

Entwickler: Linux Foundation und Mitglieder; Einsatzzweck: Linux-Desktop;

- Geschwindigsvorteil für Flash/SSD
- Aufgrund von Fragmentierung weniger für HDDs geeignet!
- Snapshots (es wird automatisch ein geeigneter (konsistenter) Zeitpunkt für den Snapshot gefunden)
- **Copy-On-Write**
- Subvolumen (mehrere Wurzelverzeichnisse, windowskompatibel)
- Prüfsumme (per CRC32C) für alle Dateien; somit kann btrfs fehlerhafte Dateien praktisch sofort erkennen und lange Dateisystemchecks vermeiden.
- Datenkompression
- integriertes RAID  
- ext-Systeme können mit btrfs-convert verlustfrei in Btrfs konvertiert werden
- Deduplizierung
- dynamische Vergabe von Inodes
- no journal
- supports the usual SSD features like TRIM
- get rid of the limits imposed by the (LVM) partition layout

disadvantages

- is highly unstable and has the potential to crash and corrupt your data if something goes wrong.
- has a copy-on-write feature that is arguably just as bad as filesystem journaling, and could (potentially) exhaust your SSD read/write limit.

## copy-on-write

When a file is changed and written back to disk, it intentionally is not written back to where it was before. Instead, it is copied and stored in an entirely new location on disk.

The modified data must be written back to the disk in any case. Btrfs merely makes sure that the data is written to previously unoccupied blocks, so the old data remains intact.

So **a history of the modified and edited files can be kept**. Btrfs will keep the references to the old file versions (inodes) somewhere they can be easily accessed. This reference is a **snapshot**.

If there are no more unused blocks to write new contents to, Btrfs will reclaim space from data blocks occupied by old file versions (Unless they are part of a snapshot).

- Copy 100MB does not require another 100MB - instead no additional space is required!
- Erst wenn die Kopie verändert wird, wird zusätzlicher Speicherplatz für die Änderungen benötigt.
- Voraussetzung: Quelle + Ziel liegen auf derselben btrfs-Partition

# CLI
## btrfs-tools oder btrfs-progs

Mount-Option mount -o ssd achtet nicht mehr darauf, die Fragmentierung des Dateisystems gering zu halten (das spart Zeit und Metadaten und ist bei SSDs nicht relevant)

## mkfs-btrfs

```sh
mkfs.btrfs -d
mkfs.btrfs --data <profile> # for the data block groups: raid0, raid1, raid5, raid6, raid10, single, dup

mkfs.btrfs -m
mkfs.btrfs --metadata <profile> # for the metadata block groups: raid0, raid1, raid5, raid6, raid10, single, dup; default = DUP, unless a SSD is detected (default = single)
```

fsck.btrfs (eigenständig)

# CONVERT

- https://fedoramagazine.org/convert-your-filesystem-to-btrfs/
- You’ll need about 20% of free disk space for a successful conversion. But for the complete backup & reinstall scenario, you might need even more.

## btrfs-convert

### 1. convert

Recreates filesystem metadata in your partition’s free disk space, while keeping all existing _ext4_ data at its current location
Unfortunately, the amount of free space required cannot be known ahead – the conversion will just fail (and do no harm) if you don’t have enough

```sh
# first reboot into the live image
sudo su -
fsck.ext4 -fyv /dev/sdXX
btrfs-convert /dev/sdXX

# roll back after a successful conversion:
btrfs-convert -r /dev/sdXX
```

### 2. mount and check

```
mount /dev/sdXX /mnt
btrfs subvolume list / -t # table view

cd /mnt
btrfs subvolume snapshot ./ ./root2
btrfs subvolume create home2
cp -a home/* home2/
```

Created two subvolumes.
- `root2` is a full snapshot of the partition
- `home2` starts as an empty subvolume and we copy the contents inside (this _cp_ command doesn’t duplicate data so it is going to be fast)
- In `/mnt` (the top-level subvolume) delete everything except 
    - `root2`
    - `home2`
    - `ext2_saved`
- Rename _root2_ and _home2_ subvolumes to _root_ and _home_.
- Inside _root_ subvolume, empty out the _home_ folder, so that we can mount the _home_ subvolume there later.

### 3. modify fstab

In order to mount the new volume after a reboot, _fstab_ has to be modified, by replacing the old _ext4_ mount lines with new ones.

You can use the command _blkid_ to learn your partition’s UUID.

```
UUID=xx / btrfs subvol=root 0 0
UUID=xx /home btrfs subvol=home 0 0
```

(Note that the two UUIDs are the same if they are referring to the same partition.)

### 4. chroot into your system

If you’ve ever done system recovery, I’m pretty sure you know these commands. Here, we get a shell prompt that is essentially _inside_ your system, with network access.

First, we have to remount the _root_ subvolume to _/mnt_, then mount the _/boot_ and _/boot/efi_ partitions (these can be different depending on your filesystem layout):

```sh
umount /mnt
mount -o subvol=root /dev/sdXX /mnt
mount /dev/sdXX /mnt/boot
mount /dev/sdXX /mnt/boot/efi
```

Then we can move on to mounting system devices:

```sh
mount -t proc /proc /mnt/proc
mount --rbind /dev /mnt/dev
mount --make-rslave /mnt/dev
mount --rbind /sys /mnt/sys
mount --make-rslave /mnt/sys
cp /mnt/etc/resolv.conf /mnt/etc/resolv.conf.chroot
cp -L /etc/resolv.conf /mnt/etc
chroot /mnt /bin/bash
ping www.fedoraproject.org
```
# SUBVOLUMES

- https://btrfs.readthedocs.io/en/latest/Subvolumes.html
- we can mount subvolumes and pass some mount options to them
- The filesystem root always has the subvolume name `/` and the subvolume ID **5**
- If you mount a Btrfs filesystem without the `subvol` or `subvolid` argument, the root subvolume with `subvolid=5` is assumed as default.
- **A subvolume is a boundary for snapshots**:
  If we separate `/` and `/home` is that we can take snapshots separately.
  A snapshot of a subvolume will only contain the content of this particular subvolume, and none of the nested subvolumes below

**@subvolume**
If you find it confusing to tell which directories are plain directories and which are subvolumes, you can feel free to adopt a special naming convention for your subvolumes. For example, you could prefix your subvolume names with an “@” to make them easily distinguishable.

## layout

- https://archive.kernel.org/oldwiki/btrfs.wiki.kernel.org/index.php/SysadminGuide.html#Layout

### flat

Subvolumes are children of the top level subvolume (ID 5), typically directly below in the hierarchy or below some directories belonging to the top level subvolume, but especially not nested below other subvolumes, for example:

```sh
toplevel         (volume root directory, not to be mounted by default)
  +-- root       (subvolume root directory, to be mounted at /)
  +-- home       (subvolume root directory, to be mounted at /home)
  +-- var        (directory)
  |   \-- www    (subvolume root directory, to be mounted at /var/www)
  \-- postgres   (subvolume root directory, to be mounted at /var/lib/postgresql)
```

Here, the toplevel mountable root directory should not normally be visible to the system. Of course, the www subvolume could have also been placed directly below the top level subvolume (without an intermediate directory) and the postgres subvolume could have also been placed at a directory var/lib/postgresql; these are just examples and the actual layout depends largely on personal taste. All subvolumes are however direct children of the top level subvolume in this scheme.

This has several implications (advantages and disadvantages):

- Management of snapshots (especially rolling them) may be considered easier as the effective layout is more directly visible.
- All subvolumes need to be mounted manually (e.g. via fstab) to their desired locations, e.g. in the above example this would look like:

```sh
LABEL=the-btrfs-fs-device   /                    btrfs subvol=/root,defaults,noatime  0  0
LABEL=the-btrfs-fs-device   /home                btrfs subvol=/home,defaults,noatime  0  0
LABEL=the-btrfs-fs-device   /var/www             btrfs subvol=/var/www,noatime        0  0
LABEL=the-btrfs-fs-device   /var/lib/postgresql  btrfs subvol=/postgres,noatime       0  0
```

- Each of these subvolumes/mountpoints can be mounted with some options being different.

This means however as well, that any typically useful mount options (for example noatime) need to be specified again for each mountpoint.

- Everything in the volume that's not beneath a subvolume that has been mounted, is not accessible or even visible. This may be beneficial for security, especially when being used with snapshots, see below.

## create


```sh
btrfs subvolume create <path>
sudo btrfs subvolume create second/third # nest subvolumes
sudo btrfs subvolume delete second # delete
```

## list

```sh
sudo btrfs subvolume list .
ID 256 gen 30 top level 5 path home
ID 257 gen 30 top level 5 path root
ID 258 gen 25 top level 257 path root/var/lib/machines
ID 259 gen 29 top level 256 path hartan/btrfs-subvolume-test/first
```

- **ID**: ID of the subvolume
- **gen**: generation counter and incremented on every Btrfs transaction
- **level**: ID of the subvolume's parent

## snapshot

```sh
# Create a snapshot of 'demo' in './demo-1'
sudo btrfs subvolume snapshot demo demo-1

# Under the hood a snapshot is a new Btrfs subvolume:

sudo btrfs subvolume list -o .
ID 259 gen 265 top level 256 path home/hartan/btrfs-snapshot-test/demo
ID 260 gen 264 top level 256 path home/hartan/btrfs-snapshot-test/demo-1
```

Btrfs snapshots work different than file copies: They keep references to current and past inodes instead. When you appended the change to the file, under the hood Btrfs allocated some more space to store the changes in and added a reference to this new data to the original inode. The previous contents remain untouched. 
You can think of this as **“storing” merely the difference between the original file and the modified version**.

### store snapshots on a different btrfs fs (backups)

- https://archive.kernel.org/oldwiki/btrfs.wiki.kernel.org/index.php/Incremental_Backup.html#Available_Backup_Tools
- https://github.com/digint/btrbk

To turn the snapshots into real backups you should store them on a different Btrfs filesystem, such as on an external drive.

```sh
sudo btrfs subvolume snapshot -r demo demo-3-ro # create a read-only snapshot
sudo btrfs send demo-3-ro | sudo btrfs receive backup-drive/ # transfer subvolume
```

### store snapshots on non-btrfs fs

In such cases you can store subvolumes in files:
```sh
sudo btrfs send -f demo-3-ro-subvolume.btrfs demo-3-ro
# The file _demo-3-ro-subvolume.btrfs_ now contains everything that is needed to recreate the _demo-3-ro_ subvolume at a later point in time.
```

### restore snapshot

```sh
# Create a read-write subvolume from the snapshot:
sudo btrfs subvolume snapshot demo-4-ro demo 
# Create a snapshot of 'demo-4-ro' in './demo'
```
## mount

To mount the subvolume we must know the path of the block device where the Btrfs filesystem subvolume resides:

```sh
findmnt -vno SOURCE /home/
# -v, --nofsroot
# Do not print a [/dir] in the SOURCE column for bind mounts or btrfs subvolumes
# -n, --noheadings
# Do not print a header line.
```

```sh
sudo mount -o subvol=home/hartan/btrfs-subvolume-test/a/2 /dev/vda3 a/1/b
```

```sh
sudo mount -o subvol=hartan/...
sudo mount -o subvolid=...
```

## compress

- https://fedoramagazine.org/working-with-btrfs-compression/