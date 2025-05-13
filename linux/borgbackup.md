# LINKS

- https://docs.borgbase.com/setup/borg/

# NOTES

* https://borgbackup.readthedocs.io/en/1.2-maint/quickstart.html
* A **Borg archive** is the result of a single backup (`borg create`). An archive stores a snapshot of the data of the files “inside” it. One can later extract or mount an archive to restore from a backup.
* **Repositories** are filesystem directories acting as self-contained stores of archives. Repositories can be accessed locally via path or remotely via ssh. 
* **push mode**: Typically the borg client connects to a backup server using SSH as a transport when initiating a backup.
* **pull mode**:  Backup a remote server to a local personal computer (the backup server initiates the connection)

```sh
xio2tw8sgn72ketq.myfritz.net
/media/frida/storage/borg_strato
strato-2024-09-29T10:54:55 # archive
ssh://borg@xio2tw8sgn72ketq.myfritz.net:151/
```

## fs permissions

* **always access the repository using the same user account** to avoid permissions issues (in your borg repository or borg cache)
* For a local repository just always use the same user to invoke borg.
* For a remote repository: always use e.g. **borg@remote_host**. You can use this from different local users, the remote user running borg and accessing the repo will always be borg.
* If you need to access a local repository from different users, you can use the same method by using ssh to [borg@localhost](mailto:borg@localhost).

## file consistency

For some systems Borg might work well enough without these precautions.  If you are simply backing up the files on a system that isn’t very active (e.g. in a typical home directory), Borg usually works well enough without further care for consistency.  Log files and caches might not be in a perfect state, but this is rarely a problem.

# CLI

## init

* ~~**init as borg user**~~

```sh
borg init user@hostname:/path/to/repo
borg init --encryption=repokey /path/to/repo # init local repo
borg init --encryption=repokey /media/borg_backup/le-guin.home
borg init --encryption=repokey /media/borg_backup/le-guin.system
borg init --encryption <encryption> # 'none', 'keyfile', 'repokey', 'authenticated', 'keyfile-blake2', 'repokey-blake2', 'authenticated-blake2'
```

```bash
sudo borg init --encryption=repokey /media/frida/borg_backup/zapata.home.frida
sudo borg key export /media/frida/borg_backup/zapata.home.frida
```
## create

- https://borgbackup.readthedocs.io/en/1.2-maint/usage/create.html

```sh
borg create /path/to/repo::Monday ~/src ~/Documents # Backup ~/src and ~/Documents directories into an archive called Monday
borg create --stats /path/to/repo::Tuesday ~/src ~/Documents # The next day create a new archive called Tuesday

sudo borg create /path/to/backup/repository::backup-{now:%Y-%m-%d} /path/to/directory
```

* **archive**

  If you have set BORG_REPO and an archive location is needed, use `::archive_name` - the repo URL part is then read from BORG_REPO.

  Archive names must not contain the `/` (slash) character. For simplicity, maybe also avoid blanks or other characters that have special meaning on the shell or in a filesystem (borg mount will use the archive name as directory name).

```sh
borg create --exclude-caches # exclude directories that contain a CACHEDIR.TAG 
```

### log

Borg writes all **log** output to stderr by default.

* https://borgbackup.readthedocs.io/en/1.2-maint/usage/general.html#logging

```sh
borg create <repo>::<archive> <files> 2>> <logfile> # capture the log output to a file
```

### output

```sh
borg create --stats /path/to/repo::<archive> <src> # output statistics about the newly created archive such as the amount of unique data (not shared with other archives)

borg create --filter AME
# ‘A’ = regular file, added
# ‘M’ = regular file, modified
# ‘E’ = regular file, an error happened while accessing/reading this file
```

### remote

* https://borgbackup.readthedocs.io/en/stable/deployment/pull-backup.html#sshfs

There are some restrictions caused by SSHFS. For example, **unless you define UID and GID mappings when mounting via `sshfs`, owners and groups of the mounted file system will probably change**, and you may not have access to those files if BorgBackup is not run with root privileges.

```sh
# Backup onto a remote host ("push" style) via ssh to port 2222,
# logging in as user "borg" and storing into /path/to/repo
borg create ssh://borg@backup.example.org:2222/path/to/repo::{fqdn}-root-{now} /
```

```sh
# Backup a remote host locally ("pull" style) using sshfs
mkdir /tmp/borg_mount_sshfs
sshfs unonweb@strato:/ borg_mount_sshfs
cd sshfs-mount
borg create /path/to/repo::example.com-root-{now:%Y-%m-%d} .
cd ..
fusermount -u sshfs-mount
```

### examples

```sh
# Backup ~/Documents and ~/src but exclude pyc files
borg create /path/to/repo::my-files \
    ~/Documents                     \
    ~/src                           \

# Backup home directories excluding image thumbnails (i.e. only
# /home/<one directory>/.thumbnails is excluded, not /home/*/*/.thumbnails etc.)
borg create /path/to/repo::my-files /home --exclude 'sh:home/*/.thumbnails

# Backup the root filesystem into an archive named "root-YYYY-MM-DD"
# use zlib compression (good, but slow) - default is lz4 (fast, low compression ratio)
borg create -C zlib,6 --one-file-system /path/to/repo::root-{now:%Y-%m-%d} /

# Use short hostname, user name and current time in archive name
borg create /path/to/repo::{hostname}-{user}-{now} ~
```

### exclude / include

- https://borgbackup.readthedocs.io/en/1.2-maint/usage/create.html
- https://borgbackup.readthedocs.io/en/1.2-maint/usage/help.html#borg-patterns
- When used from within a shell, the patterns should be quoted to protect them from expansion.

The path/filenames used as input for the pattern matching **start from the currently active recursion root**. You usually give the recursion root(s) when invoking borg and these can be either relative or absolute paths.

Starting with Borg 1.2, paths that are matched against patterns always appear relative. If you give `/absolute/` as root, the paths going into the matcher will start with `absolute/`. If you give `../../relative` as root, the paths will be normalized as `relative/`.

#### trailing slash

A **directory exclusion** pattern can end either with or without a slash `/`

```sh
# with trailing slash
borg create /home/frida --exclude '.config/Code/Cache/' # include the directory but not its content

# no trailing slash
borg create /home/frida --exclude '.config/Code/Cache' # both the directory and content will be excluded
```

#### examples

```bash
# Backup all of /home and /var/www except a few
# excluded directories
borg create -v --stats                          \
    $REPOSITORY::'{hostname}-{now:%Y-%m-%d}'    \
    /home                                       \
    /var/www                                    \
    --exclude '/home/*/.cache'                  \
    --exclude /home/Ben/Music/Justin\ Bieber    \
    --exclude '*.pyc'
```

```bash
# Backup ~/Documents and ~/src but exclude pyc files
borg create /path/to/repo::my-files \
    ~/Documents                       \
    ~/src                             \
    --exclude '*.pyc'
```

```sh
# Exclude '/home/user/file.o' but not '/home/user/file.odt':
borg create -e '*.o' backup /

# Exclude '/home/user/junk' and '/home/user/subdir/junk' but
# not '/home/user/importantjunk' or '/etc/junk':
borg create -e 'home/*/junk' backup /

# Exclude the contents of '/home/user/cache' but not the directory itself:
borg create -e home/user/cache/ backup /

# The file '/home/user/cache/important' is *not* backed up:
borg create -e home/user/cache/ backup / /home/user/cache/important

# The contents of directories in '/home' are not backed up when their name
# ends in '.tmp'
borg create --exclude 're:^home/[^/]+\.tmp/' backup /
```

## list

* https://borgbackup.readthedocs.io/en/1.2-maint/usage/list.html

```sh
borg list /path/to/repo # List all archives in the repo
borg list /path/to/repo::Monday # List the contents of the Monday archive

borg list ssh://borg@xio2tw8sgn72ketq.myfritz.net:151/media/frida/storage/borg_strato
borg list /media/borg_backup/leguin
```

## extract

* https://borgbackup.readthedocs.io/en/1.2-maint/usage/extract.html
* Currently, **extract always writes into the current working directory**
  So make sure you `cd` to the right place before calling `borg extract`.
* When parent directories are not extracted (because of using file/directory selection or any other reason), borg can not restore parent directories’ metadata, e.g. owner, group, permission, etc.

```sh
borg extract ssh://user@hostname:151/path/to/repo::archive_name
borg extract /path/to/repo::<archive> # Restore <archive> by extracting the files relative to the current directory
borg extract /path/to/repo::<archive> <path> # paths to extract; patterns are supported; 
# If you pass no paths, then all the data will be restored
borg extract -v --list repo/::25-11-2021 home/karthick/borg/source/
```

```sh
sudo borg extract --dry-run --list ssh://borg@xio2tw8sgn72ketq.myfritz.net:151/media/frida/storage/borg_strato /etc/hosts
```

## diff

```sh
borg diff /path/to/repo/::<archive1> <archive2>
```

## delete archive

```sh
borg delete /path/to/repo::Monday # Delete the Monday archive (please note that this does not free repo disk space)
borg delete --stats --progress /media/frida/storage/borg_strato::strato-2024-09-29T08:43:07
```

## prune

* https://borgbackup.readthedocs.io/en/stable/usage/prune.html

prunes a repository by deleting all archives not matching any of the specified retention options.

Important: Repository disk space is **not** freed until you run `borg compact`.

```sh
# Keep 7 end of day and 4 additional end of week archives.
# Do a dry-run without actually deleting anything.
borg prune -v --list --dry-run --keep-daily=7 --keep-weekly=4 /path/to/repo
```

## compact

This command frees repository space by compacting segments.

Use this regularly to avoid running out of space - you do not need to use this after each borg command though. It is **especially useful after deleting archives**, because only compaction will really free repository space.

```sh
borg compact /path/to/repo # Recover disk space by compacting the segment files in the repo
borg compact --progress /media/frida/storage/borg_strato
```

## check

```sh
borg check --progress /media/frida/storage/borg_strato

borg check /media/borg_backup/leguin
```

## mount

- https://borgbackup.readthedocs.io/en/stable/usage/mount.html#description
- **mounts an archive as a FUSE filesystem**
- Default behavior: use the archived user and group names of each file and map them to the system’s respective user and group ids.

```sh
borg mount -o # Extra mount options
borg mount -o allow_other # This option overrides the security measure restricting file access to the filesystem owner, so that all users (including root) can access the files.
```

```sh
borg mount ssh://user@hostname:151/path/to/repo::archive_name
borg mount <repo>::archive <mountpoint>
```

```sh
# client whose backup we want to restore
# execute as borg
mkdir /tmp/mount_borg 
borg mount -o allow_other ssh://borg@xio2tw8sgn72ketq.myfritz.net:151/media/frida/storage/borg_strato::strato-2024-09-29T10:54:55 /tmp/mount_borg

mount | grep borg # view mount options

# execute as root or unonweb
sudo rsync --archive --human-readable --delete --progress --dry-run /tmp/mount_borg/etc/ /etc
```

## umount

```sh
borg umount <mountpoint>
borg umount /tmp/mount_borg
```

## key

```sh
borg key export /media/frida/storage/borg_strato
borg key export /media/borg_backup/le-guin.home
borg key export /media/borg_backup/le-guin.system
```

# SETUP

## ssh

* https://borgbackup.readthedocs.io/en/1.2-maint/usage/serve.html#ssh-configuration

### backup server

#### authorized_keys

**restrict the use of the SSH keypair** by prepending a forced command to the SSH public key in the remote server’s authorized_keys file. 

This example will start Borg in server mode and limit it to a specific filesystem path:

```bash
command="borg serve --restrict-to-path /path/to/repo",restrict ssh-rsa AAAAB3[...]
```

#### sshd_config

```sh
sudo nano /etc/ssh/sshd_config

ClientAliveInterval 10
ClientAliveCountMax 30
```

### client

If you then run borg commands with `--lock-wait 600`, this gives sufficient time for the borg serve processes to terminate  after the SSH connection is torn down after the 300 second wait for the  keepalives to fail.

#### ssh_config

```sh
nano ~/.ssh/config # or
sudo nano /etc/ssh/ssh_config

Host xio2tw8sgn72ketq.myfritz.net
        ServerAliveInterval 10
        ServerAliveCountMax 30
```

# VARIABLES

## enivronment variables

* https://borgbackup.readthedocs.io/en/1.2-maint/usage/general.html#environment-variables

## placeholders

* https://borgbackup.readthedocs.io/en/1.2-maint/usage/help.html#borg-placeholders

Supported as values of:

* Repository/Archive URLs
* `--prefix`
* ``--glob-archives`,
*  `--comment`
* `--remote-path`


# HOW TO

## set the encryption passphrase programmatically

### setting `BORG_PASSPHRASE`

The passphrase can be specified using the `BORG_PASSPHRASE` environment variable. This is often the simplest option, but can be insecure if the script that sets it is world-readable.

Note

Be careful how you set the environment; using the `env` command, a `system()` call or using inline shell scripts (e.g. `BORG_PASSPHRASE=hunter2 borg ...`) might expose the credentials in the process list directly and they will be readable to all users on a system. Using `export` in a shell script file should be safe, however, as the environment of a process is [accessible only to that user](https://security.stackexchange.com/questions/14000/environment-variable-accessibility-in-linux/14009#14009).

## restore system

### strato

```sh
# client whose backup we want to restore
# execute as borg
mkdir /tmp/mount_borg
borg mount -o allow_other ssh://borg@xio2tw8sgn72ketq.myfritz.net:151/media/frida/storage/borg_strato::<archive> /tmp/mount_borg

# execute as root or unonweb:
sudo rsync --archive --human-readable --delete --progress --dry-run /tmp/mount_borg/ / 
# shows files which haven been deleted
sudo cp --archive --verbose /tmp/mount_borg/ / # overwrite files
```

```sh
sudo borg umount /tmp/mount_borg
```

### leguin

```sh
# execute as borg
mkdir /tmp/mount_borg
sudo borg list /media/borg_backup/leguin
sudo borg mount -o allow_other /media/borg_backup/leguin::le-guin-2024-10-07T19:51:11 /tmp/mount_borg

# execute as frida:
sudo rsync --archive --human-readable --delete --progress --dry-run /tmp/mount_borg/home/frida/code/ /home/frida/code
# shows files which haven been deleted
# attention: trailing slash at source!

sudo cp --archive --verbose /tmp/mount_borg/ / # overwrite files
```

```sh
sudo borg umount /tmp/mount_borg
```

### reinstall packages

**based on the `dpkg` log files**

```sh
grep 'install ' /var/log/dpkg.log
zgrep 'install ' /var/log/dpkg.log.*.gz # For compressed logs
grep 'install ' /var/log/dpkg.log | awk '{print $4}' | sort -u > installed-packages.txt # Create a List of Packages
xargs -a installed-packages.txt sudo apt-get install # Reinstall Packages
```

