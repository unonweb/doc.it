## snap

### basics

A snap is nothing more than a single compressed file (*.snap* extension) containing the entire file system needed for running a package.
These files are never actually decompressed and “installed,” but are mounted dynamically at run time and exposed to the user as a virtual environment.

Thus, for example, the Nextcloud snap creates its own versions of Apache and MySQL for its backend. So if, say, you want to configure a new virtual host in /etc/apache2/sites-available/ or create a new MySQL user the traditional way, you’re out of luck.

* **Ubuntu Core**, the operating system for IoT, and Edge are built from snaps to keep things secure and ensure reliable updates.
* snaps can also be used for server-focused terminal applications
* slower startup compared to Flatpak
* centralized (limited to Canonical's Snap store)
* snap applications get updated automatically by default
* needs admin privileges to install applications
* uses AppArmour for sandboxing

### paths

```sh
/snap/ # Virtual file system. Here the .snap files will be mounted when running
/snap/core/ # Looks like a regular Linux file system. It’s the virtual file system that’s being used by active snaps.

/var/snap/ # The files here contain various forms of user data and log files — the kind of data that’s generated and consumed by applications during operations.
/var/lib/snapd/snaps # The actual .snap files are kept here. Dadurch, dass in jeder Version eines snap-Pakets alle benötigten Dateien enthalten sind, können mehrere Gigabyte an Daten zusammenkommen.

/home/username/snap/ # User settings
```

### cli

```sh
snap install <pkg>

snap list # list installed snaps
snap refresh vlc # update VLC Media Player
snap revert vlc # revert VLC Media Player to the prior version 
snap remove vlc

snap changes # displays a summary of system changes performed recently.
```

```sh
snap info code # check which channels (versions) of vscode are available
snap refresh latest/edge
snap revert code # reverts the given snap to its state before the latest refresh, reactivates the previous snap revision
snap list code --all # list installed versions in your system
sudo snap revert code --revision 212 # revert to a specific revision
```

### daemons

```sh
snap services # Some more complex applications require multi-layer software stacks. Nextcloud, for instance, creates and manages its own versions of Apache, MySQL, PHP, and Redis. Each one of those “layers” is, in snap terms, called a *service*.
snap start couchdb
snap restart couchdb
snap stop --disable nextcloud.apache 
# stop Nextcloud’s Apache service and ensure that it doesn’t launch when the system reboots

systemctl status snap.nextcloud.apache
systemctl status snap.couchdb.server.service

## LOGS ##

snap logs nextcloud
snap logs nextcloud.mysql
```
