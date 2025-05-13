# LINKS

- https://linux.die.net/man/1/rsync

# RSYNC

Daten-Synchronisation von einem Quell- zu einem Zielverzeichnis; diese beiden Verzeichnisse können dabei auf unterschiedlichen Rechnern liegen; 
rsync must be installed on both the source and destination machines

* gegenüber gewöhnlichen Kopiervorgängen können Dateiberechtigungen, Zeitstempel, Eigentümer und Gruppe erhalten werden
* Delta-Algorithmus: es wird nicht die ganze Datei übertragen, sondern nur die Änderungen innerhalb der Datei
* frontends: *rsnapshot* (CLI), *Back In Time* (GUI), *Unison* (GUI), *Grsync* (GUI)

# SUMMARY

```sh
rsync --perms --times --human-readable --progress <src> <dest>

rsync --perms # Set the destination permissions to be the same as the source permissions. When --perms and --executability are both disabled, rsync's behavior is the same as that of other file-copy utilities, such as cp. To give destination files (both old and new) the source permissions

rsync --times # Transfer modification times along with the files and update them on the remote system. Note that if this option is not used, the optimization that excludes files that have not been modified cannot be  effective; in other words, a missing -t (or -a) will cause the next transfer to behave as if it used --ignore-times (-I), causing all files to be updated
```

# EXAMPLES

## trailing slash

```sh
rsync -ar --exclude $scriptname $dirpath/ $parentpath/$domain # copies the *contents* of $dirpath
rsync -ar --exclude $scriptname $dirpath $parentpath/$domain # copies $dirpath itself
```

## backup home

```sh
sudo rsync --archive --human-readable --delete --progress --exclude-from=/home/frida/code/bash/excludes /home/frida/ /media/backup-frida-on-karl #
```

## compare directories

```bash
sudo rsync --recursive --human-readable --delete --dry-run --exclude /mnt --out-format="%n" /mnt/debian /mnt/debian-snap-4
```


- do not quote `${_rsyncSources[@]}!`

```bash
declare -r RSYNC_SOURCES=(
  "--include inventory/host_vars/zapata/***"
  "--exclude inventory/host_vars/**"
  "--include playbooks/zapata.yml"
  "--exclude playbooks/*.yml"
  "frida@127.0.0.1:/media/common/code/ansible/frida/"
)

function execRsync() {
  local _cmd="rsync --archive --delete --out-format="%n" ${_rsyncSources[@]} ${RSYNC_DST}" # do not quote ${_rsyncSources[@]}!
  mapfile _changedFiles < <($_cmd) # read array from output
}
```

# NOTES

## trailing slash

It is worth mentioning that rsync gives different treatment to the **source directories** with a trailing slash (/)

*  `rsync /home/frida/docs/` **copy only the contents** to the destination directory
*  `rsync /home/frida/docs` **copy the entire directory** inside the destination directory

```sh
rsync /home/frida/docs/ /home/payload # will copy only the directory contents to the destination directory
rsync /home/frida/docs /home/payload # copies the source directory inside the destination directory
```

```sh
rsync --archive --recursive --progress --delete ~/code/ansible/franzk/git/git/ udo@franzk:/home/udo/git/ansible/git
```

## backup with rsync

* https://wiki.archlinux.org/title/Rsync#Full_system_backup

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

## sudo

```sh
# sudo rsync but normal user ssh
sudo rsync -e "sudo -u localuser ssh" -a myserver:/var/sites/example.net/ /var/sites/example.net/

# This runs rsync as root, but the -e flag causes rsync to run ssh as your local user (using sudo -u localuser), so the ssh command has access to the necessary credentials. Rsync itself is still running as root, so it has the necessary filesystem permissions.
```

# CONFIG

must be created from scratch (Debian)

```sh
sudo xed /etc/rsyncd.conf
```

# CLI

```sh
rsync -P # --partial --progress
# recommended when transferring large amounts of data
rsync --partial # keep partially transferred files
rsync --compress # used to compress data during transfers to save space; rsync -z
rsync --dirs #  transfer directories without recursing; rsync -d
```

## archive

`rsync [options] source destination`

```sh
rsync --archive # entspricht `-rlptgoD` (Unterverzeichnisse, Symlinks, Rechte, Zeiten, etc. werden kopiert bzw. beibehalten); rsync -a
rsync --recursive # copy directories recursively; rsync -r
rsync --links # copy symlinks as symlinks; rsync -l
rsync --perms # Set the destination permissions to be the same as the source permissions. When --perms and --executability are both disabled, rsync's behavior is the same as that of other file-copy utilities, such as cp. To give destination files (both old and new) the source permissions
rsync --times # Transfer modification times along with the files and update them on the remote system. Note that if this option is not used, the optimization that excludes files that have not been modified cannot be  effective; in other words, a missing -t (or -a) will cause the next transfer to behave as if it used --ignore-times (-I), causing all files to be updated
# rsync -t
rsync --group # preserve group; rsync -g
rsync --owner # preserve owner (super-user only); rsync -o
rsync -D # same as --devices --specials
```

## preserve / change

### times

```sh
rsync --times --atimes --crtimes
rsync --times # preserve modification times
rsync --atimes # preserve access times
rsync --crtimes # preserve create times
```

```sh
rsync --perms # preserve permissions
rsync --chmod=<chmod> # affect file and/or directory permissions
rsync --xattrs # preserve extended attributes; rsync -X
rsync --acls # preserve ACLs (implies --perms); rsync -A
```

## user / group

```sh
rsync --owner # preserve owner (super-user only)
rsync --group # preserve group
rsync --chown=<user>:<group> # username/groupname mapping
rsync --numeric-ids # don't map uid/gid values by user/group name
```

## links

```sh
rsync --links #  Add symlinks to the transferred files instead of noisily ignoring them with a "non-regular file" warning for each symlink encountered. You can alternately silence the warning by specifying --info=nonreg0. The default handling of symlinks is to recreate each symlink's unchanged value on the receiving side.
rsync --hard-links #  preserve hard links; rsync -H
```

## include / exclude

- https://thelinuxcode.com/how-to-exclude-files-and-directories-with-rsync/

`rsync` include exclude pattern examples:

```sh
"*" # means everything
"dir1" # transfers empty directory [dir1]
"dir*" # transfers empty directories like: "dir1", "dir2", "dir3", etc...
"file*" # transfers files whose names start with [file]
"dir**" # transfers every path that starts with [dir] like "dir1/file.txt", "dir2/bar/ffaa.html", etc...
"dir***" # same as above
"dir1/*" # does nothing
"dir1/**" # does nothing
"dir1/***" # transfers [dir1] directory and all its contents like "dir1/file.txt", "dir1/fooo.sh", "dir1/fold/baar.py", etc...
```

think of the `include` pattern as a a way of overriding `exclude` pattern

```sh
rsync --exclude=<pattern> # exclude files matching PATTERN
rsync --exclude-from=<file> # read exclude patterns from FILE
rsync --include=<pattern> # don't exclude files matching PATTERN
rsync --include-from=<file> # read include patterns from FILE
```

### exclude

* the path to the excluded directoriy is relative to that of our source path
* to exclude the directory content but not the directory itself use `dir1/*`
* include only works along with exclude
* Rule: **First include, then exclude**
  **first plus, then minus**

```sh
# here "/path/to/src/linuxconfig" is excluded:
--exclude linuxconfig /path/to/src/ /path/to/dest/

# exclude the directory content but not the directory itself:
--exclude 'dir1/*' /path/to/src/ /path/to/dest/ 

# exclude all .jpg files:
--exclude '*.jpg*' /path/to/src/ /path/to/dest/ 

# specifies the files and directories to exclude in a file:
--exclude-from=exclude-file.txt /path/to/src/ /path/to/dest/ 
```

curly braces

```bash
# list the files and directories you want to exclude in `{}` 
# separated by a comma: 

--exclude={'file1.txt','dir1/*','dir2'} /path/to/src/ /path/to/dest/ 

--exclude={".git"} # did not work!
--exclude=".git" # worked!
```

* exclude list must be given without quotation 
### exclude-from

```sh
# backup_exclude.txt
- delme
- Downloads
- studio-link
+ .ssh # explicitly include hidden ssh
- .* # exclude all hidden files that have not been included so far
+ Nextcloud/Keepass # explicitly include this subdir
- Nextcloud/* # but exclude all other s
```

## output

```sh
rsync --human-readable # output numbers in a human-readable format; rsync -h
rsync --verbose; rsync -v
rsync --itemize-changes
rsync -i # output a change-summary for all updates
rsync --progress # zeigt den Fortschritt des Kopiervorganges an
rsync --stats # give some file-transfer stats
```

### out-format

```sh
rsync --out-format=""%n" # the filename
%o the operation, which is "send", "recv", or "del."
```

- %a the remote IP address
- %b the number of bytes actually transferred
- %B the permission bits of the file (e.g. rwxrwxrwt)
- %c the total size of the block checksums received for the basis file (only when sending)
- %f the filename (long form on sender; no trailing lq/rq)
- %G the gid of the file (decimal) or lqDEFAULTrq
- %h the remote host name
- %i an itemized list of what is being updated
- %l the length of the file in bytes
- %L the string lq -> SYMLINKrq, lq => HARDLINKrq, or lqrq (where **SYMLINK** or **HARDLINK** is a filename)
- %m the module name
- %M the last-modified time of the file
- %n the filename (short form; trailing lq/rq on dir)
- %o the operation, which is lqsendrq, lqrecvrq, or lqdel.rq (the latter includes the trailing period)
- %p the process ID of this rsync session
- %P the module path
- %t the current date time
- %u the authenticated username or an empty string
- %U the uid of the file (decimal)

## delete / backup

```sh
rsync --delete 
# entfernt Dateien im Zielverzeichnis die mittelerweile im Quellverzeichnis nicht mehr vorhanden sind; 
# useful for mirroring
# wird allerdings z.B. irrtümlich eine Datei gelöscht und anschließend rsync ausgeführt, ist die gelöschte Datei auch im Zielverzeichnis nicht mehr vorhanden (Lösung: rdiff-backup)
# rsync -d
rsync --backup # sorgt dafür, dass durch die Option `--delete` gelöschte sowie alle veränderten Objekte gesichert werden; 
# rsync -b
rsync --backup-dir=path # gibt ein Verzeichnis für die gelöschten und geänderten Objekte an
```

## multiple sources

```sh
rsync --archive --human-readable --delete --progress ./_input/assets/img/ ./_input/assets/fonts/ ./_build/assets
```

## remote

```sh
rsync --remote-option=OPT # -M  send OPTION to the remote side only
```

## over ssh

* By default, rsync is configured to use ssh.
* If you haven’t set a passwordless SSH login to the remote machine, you will be asked to enter the user password.

```sh
rsync -e # allows you to choose a different remote shell
rsync --archive -e "ssh -p 15108" <localPath> <user@host>:<remotePath>

# Local to Remote
rsync [options] -e ssh <localPath> <user@host>:<remotePath>

# Remote to Local: 
rsync [options] -e ssh <user@host>:<remotePath> <localPath>
```

## diff algorithm

`rsync` has three ways to decide if a file is outdated:

1. Compare the size of source and destination
2. Compare the timestamp of source and destination
3. Compare the static checksum of source and destination

By default, `rsync` uses only 1 and 2 (files are transferred if ***either size or last-modified time*** has changed)

Both 1 and 2 can be acquired together by a single `stat`, whereas 3 requires reading the entire file (this is independent from reading the file for transfer). Assuming only one modifier is specified, that means the following:

- By using `--size-only`, only 1 is performed - timestamps and checksum are ignored
  A file is copied unless its size is identical on both ends.
- By using `--ignore-times`, neither of 1, 2 or 3 is performed. A file is always copied.
- By using `--checksum`, 3 is used in *addition* to 1, but 2 is *not* performed. A file is copied unless size and checksum match. The checksum is only computed if size matches.

```sh
rsync --size-only # changes to just looking for files that have changed in size; useful when starting to use rsync after using another mirroring system which may not preserve timestamps exactly

rsync --ignore-times # ignore both the time and size (does more than its name implies). Normally rsync will skip any files that are already the same size and have the same modification timestamp. This option turns off this "quick check" behavior, causing all files to be updated. This option can be confusing compared to --ignore-existing and --ignore-non-existing in that that they cause rsync to transfer fewer files, while this option causes rsync to transfer more files.
```

```sh
rsync --update # überspringt Dateien, die am Zielort neuer sind; rsync -u
rsync --existing #  skip creating new files on receiver

rsync -I # don't skip files that match size and time

rsync --inplace # update destination files in-place
rsync --append #  append data onto shorter files
rsync --append-verify # --append w/old data in file checksum
rsync --whole-file
rsync -W # copy files whole (w/o delta-xfer algorithm)
```

## ignore existing

```sh
rsync --ignore-non-existing # skip creating files (including directories) that do not exist yet on the destination.  If this option is combined with the --ignore-existing option, no files will be updated (which can be useful if all you want to do is delete extraneous files).
rsync --ignore-existing # skip updating files that already exist on the destination (this does not ignore existing directories, or nothing would get done).
```

# DAEMON MODE

* not recommended for the transfer of files across unsecured networks (the Internet)
* not encrypted
* synchronize data in internal networks

```sh
rsync --daemon
```

# SCRIPTS

## backup script for SSH password-based authentication

This script essentially wraps the `rsync` operation in a  retry loop, handling password-based SSH authentication and displaying  progress information. It continues to retry the operation until it  succeeds or until you manually terminate the script.

```sh
#!/bin/bash

# Define source and destination directories
source_directory="/path/to/source"
destination_directory="/path/to/destination"

# Define the SSH password
ssh_password="YourRootPassword"

# Loop until rsync completes successfully
while true; do
    # Perform the rsync operation with SSH password authentication and progress display
    rsync -avz --partial --append --progress -e "sshpass -p '$ssh_password' ssh -p 2200 -o StrictHostKeyChecking=no" root@your_remote_vps_ip:"$source_directory" "$destination_directory"

    # Check the exit status of rsync
    if [ $? -eq 0 ]; then
        echo "rsync completed successfully."
        break  # Exit the loop if rsync is successful
    else
        echo "rsync failed or was interrupted. Retrying in 5 seconds..."
        sleep 5  # Wait for 5 seconds before retrying
    fi
done
```

## backup script for SSH key-based authentication

```sh
#!/bin/bash

# Define source and destination directories
source_directory="/path/to/source"
destination_directory="/path/to/destination"

# Loop until rsync completes successfully
while true; do
    # Perform the rsync operation with SSH key-based authentication and progress display
    rsync -avz --partial --append --progress -e "ssh -i /path/to/your/private/key -p 2200 -o StrictHostKeyChecking=no" root@your_vps_ip:"$source_directory" "$destination_directory"

    # Check the exit status of rsync
    if [ $? -eq 0 ]; then
        echo "rsync completed successfully."
        break  # Exit the loop if rsync is successful
    else
        echo "rsync failed or was interrupted. Retrying in 5 seconds..."
        sleep 5  # Wait for 5 seconds before retrying
    fi
done
```
