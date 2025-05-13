
```sh
cat /proc/filesystems # lists all the filesystems your kernel supports
```
# SOLUTIONS

### map ata device number to logical device name

```sh
# Solution 1:
# Find out what device stands for ata1:
ata=1; ls -l /sys/block/sd* | grep $(grep $ata /sys/class/scsi_host/host*/unique_id | awk -F'/' '{print $5}')

# Solution 2:
ls -l /sys/block/sd* | sed 's/.*\(sd.*\) -.*\(ata.*\)\/h.*/\2 => \1/'
```

### clone partition

For example you want to clone `/dev/sda1` to `/dev/sdb`

* **gparted** and **gddrescue**
* use **gparted** to create **/dev/sdb1** partition with the same size as **/dev/sda1** or larger.

### integrate new disk

1. ​	Hardware verbinden
2. ​	**lsblk**: ist das Blockgerät ansprechbar?
3. ​	**fdisk/gdisk**: Partitionstabelle anlegen
4. ​	**mk2fs**: Dateisystem anlegen
5. ​	**fstab**: automatische mountpoint anlegen
6. ​	**mount**

## sd card

```sh
sudo fdisk -l | grep -Ei 'card|$' # highlights all entries with 'card'
# get the device path: /dev/sdb1
mount -t vfat /dev/sdb1 /mnt/SD # mount it
```

# TUTORIALS

## moving /var to a new partition

1. Unallocated space to create the partitions for each mountpoint (/var, /home, /tmp). Use Gparted for this.
2. Then you need to create the filesystems for those partitions (can be done with Gparted too) or use: mkfs.ext4 /dev/sdaX for example to create a new ext4 filesystem on the /dev/sdaX device (replace /dev/sdaX with your own device)
3. Mount the new filesystem under /mnt: mkdir /mnt/var; mount /dev/sdaX /mnt/var
4. Go to single-user mode so that there is no rw activity on the directory during the process: init 1
5. Enter your root password.
6. Backup data in var only (not the /var directory itself): cp -ax /var/* /mnt/var
7. Rename the /var directory after your data has been transferred successfully: cd /; mv var var.old
8. Make the new var directory: mkdir var
9. Unmount the new partition: umount /dev/sdaX
10. Remount it as /var: mount /dev/sdaX /var
11. Edit /etc/fstab file to include the new partition, with /var being the mount point, so that it will be automatically mounted at boot: /dev/sdaX /var ext4 defaults 0 0
12. Finally return to multitasking mode.

# FILESYSTEM HIERARCHY STANDARD

## comparison

### /usr/local vs /opt

- https://www.tech-island.com/techtalk/linux-der-unterschied-zwischen-usrlocal-und-opt

`/usr/local` 
- software not managed by the system packager
- but still following the standard unix deployment rules.
  That's why you have `/usr/local/bin`, `/usr/local/sbin` `/usr/local/include` etc...

`/opt` 
- software that doesn't follow this 
- software deployed in a monolithic fashion
- "Windows" style

## hierarchy

### /opt

- generally for programs that are self-contained or need to be isolated from the rest of the system for some reason (low and medium interaction honeypot programs, for example).

### /var

* variable data files
* spool directories and files
* administrative and logging data
* transient and temporary files

If `/var` cannot be made a separate partition, it is often preferable to move `/var` out of the root partition and into the `/usr` partition.  (This is sometimes done to reduce the size of the root partition or when space runs low in the root partition.)  However, `/var` must not be linked to `/usr` because this makes separation of `/usr` and `/var` more difficult and is likely to create a naming conflict.  Instead, link `/var` to `/usr/var`.

- 88M	/var/lib/dpkg
- 119M	/var/lib/apt
- 1,4G	/var/lib/machines
- 5,8G	/var/lib/flatpak
- 13G     /var/

### /home

Some see this directory as being an area for group files as well. 

> on large systems (especially when the /home directories are shared amongst many hosts using NFS) it is useful to subdivide user home directories.  Subdivision may be accomplished by using subdirectories such as  /home/staff, /home/guests, /home/students, etc."

- 423M	/home/frida/node_modules
- 626M	/home/frida/.npm
- 925M	/home/frida/.cache
- 2,9G	/home/frida/.config
- 2,9G	/home/frida/.thunderbird
- 3,8G	/home/frida/.var
    - 67M	/home/frida/.var/app/org.gnome.Maps
    - 498M	/home/frida/.var/app/org.mozilla.firefox
    - 3,0G	/home/frida/.var/app/org.mozilla.Thunderbird
- 6,2G	/home/frida/downloads
- 9,2G	/home/frida/.local
    - 2,2G	/home/frida/.local/share/Trash
    - 6,4G	/home/frida/.local/share/flatpak
- 28G	    /home/frida
### /usr

* the second major section of the filesystem
* shareable, **read-only** data
* Data here should only change for administrative reasons (e.g. the installation of new  packages.)
* That means that `/usr` should be shareable between various FHS-compliant hosts and must not be written to
* Any information that is host-specific or varies with time is stored elsewhere

- 3,1G     /usr/share
- 5,3G	/usr/lib
- 9,1G	    /usr

### `/srv`

> contains site-specific data which is served by this system

- is an acceptable and often-preferred location for group files
