# rsnapshot

A filesystem snapshot utility based on **rsync**. It allows you to easily create periodic snapshots of local machines, and remote machines **over ssh**.

The good thing about rsnapshot is it makes **extensive use of hard links** whenever possible, to greatly reduce the disk space required.

Since rsnapshot only keeps a fixed number of snapshots, the amount of disk space used will not continuously grow.

Rnapshot is a free, and open source backup application that **supports incremental backups**. It is written entirely in Perl, and it should work on most Unix-like systems.

- takes incremental snapshots of local and remote filesystems
- allows you to make several levels of snapshots (alpha, beta, gamma, etc.) and assign any names to them, as well as rotate them, removing obsolete ones.

# cli

```sh
sudo apt install rsnapshot rsync
```

```sh
sudo rsnapshot configtest #
sudo rsnapshot -t # test, show shell commands that would be executed
sudo rsnapshot -t hourly
sudo rsnapshot -t daily
sudo rsnapshot -t weekly
sudo rsnapshot -t monthly
```

### start

Run the shortest backup strategy to create a first full snapshot. In our example, that's the daily backup (adjust accordingly):

```sh
sudo rsnapshot daily
```

# levels & structure

```sh
rsnapshot alpha
```

Every time this is run, the following will happen:

- `snapshot_root/alpha.5/` will be deleted, if it exists
- `snapshot_root/alpha.{1,2,3,4}` will all be rotated +1, if they exist
- `snapshot_root/alpha.0/` will be copied to `snapshot_root/alpha.1/` using hard links.

Each backup point (explained below) will then be rsynced to the corresponding directories in `snapshot_root/alpha.0/`

Backup levels must be specified in the config file in order, from most frequent to least frequent. The first entry is the one which will be synced with the backup points. The subsequent backup levels (e.g., beta, gamma, etc) simply rotate, with each higher backup level pulling from the one below it for its .0 directory.

```sh
rsnapshot beta # the beta snapshot is created from the oldest copy of the alpha level (alpha.5) 
```

# config

```sh
sudo nano /etc/rsnapshot.conf
```

- **parameters must be separated by tabs**

```sh
snapshot_root	<path> # local filesystem path to save all snapshots
no_create_root 1 # to avoid that rsnapshot automatically creates a backup folder

ssh_args	-p 22222 # use unusual port
```

### retain

```sh
retain	<intervall>	<number>
# <intervall> refers to the name of this backup level
# <number> is the number of snapshots for this type of interval that will be retained; 
# the value of <name> will be the command passed to rsnapshot to perform this type of backup.

retain  daily   6
retain  weekly    7
retain  monthly   4
```

### backup

* welche Verzeichnisse wohin gesichert werden

```sh
backup  /home/          localhost/
backup  /etc/           localhost/
backup  /usr/local/     localhost/
# localhost/ will be a sub-directory in the snapshot_root that you set up earlier

backup  /   .   exclude=tmp,exclude=proc,exclude=mnt,exclude=sys
```

```sh
backup root@example.com:/home/ example.com/    +rsync_long_args=--bwlimit=16,exclude=core
backup root@example.com:/etc/  example.com/    exclude=mtab,exclude=core
```

As you can see, you can pass extra rsync parameters (the `+` appends the parameter to the default list – if you remove the `+` sign you override it) and also exclude directories.

### logging

```sh
sudo touch /var/log/rsnapshot.log
```

```sh
verbose     5
loglevel    5
logfile     /var/log/rsnapshot.log
```

### cmds

```sh
cmd_preexec		<path> 
# full path (plus any arguments) to preexec script (optional). 
# This script will run immediately before each backup operation (but not any rotations)

cmd_postexec 	<path> 
# full path (plus any arguments) to postexec script (optional).
# This script will run immediately after each backup operation (but not any rotations)

cmd_rsync           /usr/bin/rsync
cmd_du              /usr/bin/du
cmd_rsnapshot_diff  /usr/bin/rsnapshot-diff
cmd_ssh				/usr/bin/ssh
```

# scheduling

### systemd

#### systemd timer unit daily

```sh
sudo nano /etc/systemd/system/rsnapshot-daily.timer
```

```sh
[Unit]
Description=rsnapshot daily backup

[Timer]
# 05:30 is the clock time when to start it
OnCalendar=05:30
Persistent=true
Unit=rsnapshot@daily.service

[Install]

WantedBy=timers.target
systemctl enable --now rsnapshot-daily.timer #
```

# recover

just copy the files from the snapshot root

```sh
/path/to/rsnapshot/backup/folder/daily.0
/path/to/rsnapshot/backup/folder/daily.1
/path/to/rsnapshot/backup/folder/daily.2
/path/to/rsnapshot/backup/folder/weekly.0
/path/to/rsnapshot/backup/folder/weekly.1
/path/to/rsnapshot/backup/folder/weekly.2
/path/to/rsnapshot/backup/folder/weekly.3
/path/to/rsnapshot/backup/folder/weekly.4
/path/to/rsnapshot/backup/folder/weekly.5
/path/to/rsnapshot/backup/folder/monthly.0
/path/to/rsnapshot/backup/folder/monthly.1
```

```sh
sudo rsync -avr $SOURCE_PATH $DESTINATION_PATH
```

For example, the following command will restore the weekly snapshot of the entire file system from 3 weeks ago to the root `/` of the server:

```sh
sudo rsync -avr /path/to/rsnapshot/backup/folder/weekly.3/ /
```