# Backup Linux

## content

* https://wiki.archlinux.org/title/Rsync#Full_system_backup

* **include**: 
  * /etc
  * /var
    * exclude /var/run
    * exclude /var/cache
    * exclude /var/tmp
    * exclude /var/lock
  * /home
  * /opt
  * /root
* **include maybe:**
  * /boot (Kernel)
  * /lib
  * /sbin (Programme für Root)
* **exclude:** 
  * /proc
  * /dev
  * /mnt
  * /cdrom
  * /media
  * /run
  * /sys
  * /tmp
  * /lost+found

## not necessary to include binaries

```sh
# reinstall packages based on the `dpkg` log files:
grep 'install ' /var/log/dpkg.log
zgrep 'install ' /var/log/dpkg.log.*.gz # For compressed logs
grep 'install ' /var/log/dpkg.log | awk '{print $4}' | sort -u > installed-packages.txt # Create a List of Packages
xargs -a installed-packages.txt sudo apt-get install # Reinstall Packages
```

## example rsync

* The directories `/dev`, `/proc`, `/sys`, `/tmp`, and `/run` are included in the above command, but the *contents* of those directories are excluded. This is because they are populated on boot, but the directories themselves are not created.

```sh
rsync -aAXHv \
--exclude='/dev/*' \
--exclude='/proc/*' \
--exclude='/sys/*' \
--exclude='/tmp/*' \
--exclude='/run/*' \
--exclude='/mnt/*' \
--exclude='/media/*' \
--exclude='/lost+found/' \
/ \
/path/to/backup
```

## tools

```sh
dd if=<device> of=<path.backup> # es ist auch möglich auf diese Weise ganze Betriebssysteme zu klonen
tar -cf <path.backup> /home # sichert das Home-Verzeichnis nach *path*.backup
```

- ​	**bacula**: cross-platform
- ​	**amanda**: Advanced Maryland Automatic Network Disk Archiver
- ​	**BackupPc**: cross-platform; full and incremental backups; file compression; Lizenz: GPL

- ​	**Déjà Dup**
- ​	**Back In Time:** verwendet rsync; GUI
- ​	**Borg Backup**: CLI; Deduplikation; Verschlüsslung; SSH; Komprimierung; unabhängig vom Dateisystem

## backup with rsync

* https://ostechnix.com/backup-entire-linux-system-using-rsync/
* `rsync` is better than disk cloning with `dd`. Because It doesn't matter if your hard drive is different size, or use different filesystem. It will work regardless of hard disk's size and the underlying file system.
* To restore the backup, just reverse the source and destination paths
* If the system is heavily used a snapshot-based backup is the better approach. Because the system will  get "froze" before the backup process starts and get it "unfreeze" when  the backup process finishes, so all the files are consistent.

### local

```sh
# backup entire local filesystem
sudo rsync -aAXv / --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found"} /mnt

# --xattrs # preserve extended attributes; rsync -X
# --acls # preserve ACLs (implies --perms); rsync -A
```

### remote

#### issues

- **root rights**
1. für einen Kopiervorgang außerhalb von /home werden Root-Rechte benötigt
  
1. aus Sicherheitsgründen sollte Root jedoch keinen direkten SSH-Zugang haben

#### solution a)

Einen Benutzer *rsync* anlegen und unter `/etc/sudoers` konfigurieren:

```sh
sudo visudo /etc/sudoers
rsync ALL = NOPASSWD: /usr/bin/rsync
```

on the source machine, specify that `sudo rsync` shall be used:

```sh
rsync --rsync-path="sudo rsync" # specify the rsync to run on remote machine
```

```sh
# remote >> local
rsync -avd -e ssh --numeric-ids user@host:<source-path> <dest-path>
# local >> remote
rsync -avd -e --numeric-ids ssh <source-path> user@host>:<dest-path>

# rsync -e shh wählt die remote shell aus
# --numeric-ids verhindert, dass die UID/GID des Besitzers an den entfernten Rechner angepasst wird
# --rsync-path=<path> # specifies the rsync to run on the remote machine
```

#### solution b)

* https://wellquite.org/posts/backups/

The question is

**Should the backup be initiated on the backup machine, and *pull* data from the server; or should it be initiated on the server, and *push* data to the backup machine?** I don’t want to add `setuid` binaries to the servers, and I don’t want to allow `root` to log-in via `ssh` on the servers. So really that means the backup must be initiated on the server, and *push* data out.

So, initial setup:

1. On the backup machine, create a new user, `backup`.
2. Make sure `root` on the server has an ssh key-pair (`ssh-keygen -t ed25519` etc).
3. Make sure the public key side of that is in the `authorized_keys` for the `backup` user on the backup machine.

#### authorized keys

```
command="/home/backup/bin/checkssh.sh",no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty ssh-ed25519 AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA comment
```