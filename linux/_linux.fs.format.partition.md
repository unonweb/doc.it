
# SOLUTIONS

```sh
## create partitions

sudo parted -l # lists partition layout on all block devices
sudo parted /dev/sda mklabel gpt
sudo parted -a optimal /dev/sda mkpart primary ext4 0% 100% 
# sets the default optimal alignment type
# makes a standalone (i.e. bootable, not extended from another) partition, using the ext4 filesystem
```

## f2fs

```sh
sudo apt install f2fs-tools

fsck.f2fs
mkfs.f2fs
resize.f2fs
```

Since Linux 4.2, F2FS natively supports file encryption. Encryption is applied at the directory level, and different directories can use different encryption keys. This is different from both dm-crypt, which is block-device level encryption, and from eCryptfs, which is a stacked cryptographic filesystem. To use F2FS's native encryption support, see the fscrypt article. Create the file system with

```sh
mkfs.f2fs -l mylabel -O extra_attr,inode_checksum,sb_checksum /dev/sdxY
# a minimum recommended set of options which help f2fs.fsck to detect and to fix some types of filesystem corruption
mkfs.f2fs -l mylabel -O extra_attr,inode_checksum,sb_checksum,encrypt /dev/sdxY

# or add encryption capability at a later time with 
fsck.f2fs -O encrypt /dev/sdxY
```

# MKFS

```sh
mkfs.exfat -n <name> # 
```

# FDISK

```sh
sudo fdisk -l /dev/sdc
sudo fdisk --list /dev/sdc # zeigt die Partitionstabelle von device bzw. aller Geräte, die unter /proc/partitions hinterlegt sind.
```

# FSCK

```sh
# fsck.fat
fsck.fat -w # write to disk immediately.
fsck.fat -r # check interactively (ask you what to do to when encountering errors). On newer versions = default
fsck.fat -l # list the filenames processed.
fsck.fat -a # automatically fix errors. Do not use it, if You want to have more control over fixing possible errors.
fsck.fat -v # verbose mode. Generates slightly more output.
fsck.fat -t # mark unreadable clusters as bad.
```

# GDISK

Hex Code Angabe korrekt;

```sh
sudo gdisk -l /dev/sdc
sudo gdisk --list /dev/sdc # zeigt die Partitionstabelle von <device>
```

a script-oriented tool for partitioning any block device

```sh
sfdisk -d
sfdisk --dump # device dumps the partitions of a device in a format that is usable as input to sfdisk
sfdisk -d <device1> | sfdisk <device2> kopiert die Partitionstabelle von *device1* auf *device2*
```

# PARTED

gparted is the GUI tool

```sh
sudo apt install parted

sudo parted -l # lists partition layout on all block devices
sudo parted /dev/sda mklabel gpt
sudo parted -a optimal /dev/sda mkpart primary ext4 0% 100% 
# sets the default optimal alignment type
# makes a standalone (i.e. bootable, not extended from another) partition, using the ext4 filesystem
```