# filesystem independent

Some of these options are only useful when they appear in the */etc/fstab* file.

Some of these options could be enabled or disabled by default in  the system kernel. To check the current setting see the options in  `/proc/mounts`.

```sh
man mount # --> Filesystem Independent Mount Options
```

```sh
defaults # Use default options: rw, suid, dev, exec, auto, nouser, async, and relatime. 

auto # Can be mounted with the -a option.
noauto # Can only be mounted explicitly (i.e., the -a option will not cause the filesystem to be mounted). 

exec # Permit execution of binaries
noexec # Do not allow direct execution of any binaries on the mounted filesystem

nofail # Do not report errors for this device if it does not exist

suid # Allow set-user-identifier or set-group-identifier bits to take effect
nosuid # Do not allow set-user-identifier or set-group-identifier bits to take effect. (This seems safe, but is in fact rather unsafe if you have suidperl(1) installed.) 

dev # Interpret character or block special devices on the filesystem.
nodev # Do not interpret character or block special devices on the file system. 

ro # Mount the filesystem read-only
rw # Mount the filesystem read-write

async # All I/O to the filesystem should be done asynchronously
sync # All I/O to the filesystem should be done synchronously. In case of media with limited number of write cycles (e.g. some flash drives) "sync" may cause life-cycle shortening. 

nouser # Forbid an ordinary user to mount the filesystem. This is the default.
users # Allow every user to mount and unmount the filesystem. This option implies the options noexec, nosuid, and nodev (unless overridden by subsequent options, as in the option line users,exec,dev,suid). 
```

# filesystem specific

```sh
man mount # --> Filesystem Specific Mount Options
```

# ext2

```sh
grpquota|noquota|quota|usrquota 
# The usrquota (same as quota) mount option enables user quota support on the file system.
# grpquota enables group quotas support. 
# You need the quota utilities to actually enable and manage the quota system.
```

# ext3

```sh
user_xattr # Enable Extended User Attributes. See the attr(5) manual page. 
acl # Enable POSIX Access Control Lists.
```

# ext4

```sh
man ext4
```

```sh
resize # Allows to resize filesystem to the end of the last existing block group, further resize has to be done with resize2fs either online, or offline. It can be used only with conjunction with remount. 
```

# nfs

```sh
sudo mount <options> <nfs-server>:</path-to-exported-directy> </mount-point-on-client>

sudo mount -t nfs hostname:/ressource mountpoint # t = types 
sudo mount -t nfs little_foot:/home/music /media/little_foot/music
sudo mount -t nfs karl:/home/backup-frida /media/backup-frida-on-karl
sudo umount <path>
```

# The Loop Device

One further possible type is a mount via the loop device. For example, the command

```sh
mount /tmp/fdimage /mnt -t vfat -o loop=/dev/loop3 # will set up the loop device */dev/loop3* to correspond to the file */tmp/fdimage*, and then mount this device on */mnt*.sh
```

This type of mount knows about four options, namely **loop**, **offset**, **sizelimit** and **encryption**, that are really options to **losetup**. (These options can be used in addition to those specific to the filesystem type.)

If no explicit loop device is mentioned (but just an option '**-o loop**' is given), then **mount** will try to find some unused loop device and use that.

# BIND MOUNTS

**Mount an already-mounted file system to another location within the file system.**
A _bind mount_ is an alternate view of a directory tree.

Unlike a hard link or symbolic link, a bind mount doesn't affect what is stored on the filesystem. It's a property of the live system.

```sh
mount --bind /source/path /dest/path
# /source/path is not a disk partition like in the case of an on-disk filesystem, but an existing directory
# if /dest/path was not empty, its previous content is now hidden
```