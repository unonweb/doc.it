---
title: NFS
tags: ['nfs','linux']
---

NFS
==========

* Network File System; ein Protokoll für den Zugriff auf Dateien über das Netzwerk
* a distributed file system that provides transparent access to remote disks
* For the user, NFS means that he or she doesn’t have to log into other systems to access files.

## use cases

Instead of duplicating common directories such as */usr/local* on every system, NFS provides a single copy of the directory that is shared by all systems on the network.

## RPC
Remote Procedure Calls  
Verbindungsaufbau zum NFS-Server erfolgt per *RPC*  
The NFS service needs the Remote Procedure Call service running to work: `systemctl start rpcbind.service`

remember
------------

```sh
scp <file> <user>@<hosts>:<path>  # führt dazu, dass die kopierte Datei dem Benutzer gehört, der für den Login angegeben wird!

sudo cp <file> <nfs-destination> # führt zu *permission denied*, wenn auf dem Server nicht explizit `no_root_squash` gesetzt ist. 

# By default, NFS translates requests from a root user remotely into a non-privileged user on the server. This was intended as security feature to prevent a root account on the client from using the file system of the host as root. no_root_squash disables this behavior for certain shares.
```

CLIENT
==========

## install
```sh
sudo apt install nfs-common # Ubuntu
yum -y install nfs-utils # RHEL/CentOS
```

## mount 

### manually

```sh
sudo mount <options> <nfs-server>:</path-to-exported-directy> </mount-point-on-client>

sudo mount -t nfs hostname:/ressource mountpoint # t = types 
sudo mount -t nfs little_foot:/home/music /media/little_foot/music
sudo mount -t nfs karl:/home/backup-frida /media/backup-frida-on-karl
sudo umount <path>
```

### persistently

### check

```sh
mount | grep nfs
```


SERVER
========

`nfs-server.service`

```sh
# firewall
sudo ufw allow from <ip> to any port nfs
```

## install

```sh
apt install nfs-kernel-server # currently the recommended NFS server for use with Linux, featuring NFSv3 and NFSv4; it's significantly faster and usually more reliable than the user-space NFS servers
```

## authentification

#### NFSv3
Authentifizierung des Client-Rechners

#### NFSv4.x.
Authentifizierung des Benutzers (wie bei SMB)

Wird **Kerberos** zur Authentifizierung eingesetzt, müssen folgende Bedingungen erfüllt sein, dass eine NFS-Operation ausgeführt werden kann:

* Der NFS-Server muss gegenüber Kerberos authentifiziert sein (nicht benutzerbezogen).
* Ein Client muss gegenüber dem Kerberos Server authentifiziert sein (nicht benutzerbezogen).
* Der Benutzer, der am Client angemeldet ist, muss gegenüber Kerberos authentifiziert sein.

### security / issues

#### Problem: UID

Die Dateien auf dem NFS-Share gehören dann dem Besitzer des NFS-Shares.

`ll -n` # list numeric user and group IDs

Die Zugriffskontrolle im Dateisystem basiert auf den UIDs und GIDs; wenn z.B. unter dem exportierten Verzichnis /public eine Datei einem User mit der UID 1000 gehört und ein User mit derselben UID über das Netzwerk zugreift, hat dieser dieselben Rechte!

Aus diesem Grund werden Netzwerkbenutzer beim Zugriff auf exportierte Verzeichnisse mit *all_squash* zu nobody degradiert und haben dann dieselben Rechte wie others.


config
-------------

Exporting directories are listed in: `/etc/exports` (Freigabe von Dateien via NFS/Zugriffssteuerung)

```sh
# changes: after all changes do:
exportfs -ra
```

#### files

```sh
sudo nano /var/lib/nfs/etab # master export table; this file is read by *rpc.mountd* when a client sends an nfs mount request
sudo nano /etc/exports
ll /etc/exports.d/
# NFS server export table contains a table of local physical file systems on an NFS server that are accessible to NFS clients man exports
```

#### syntax

```sh
# /etc/exports
path-to-export client(options)

# client may be:
/mnt/nfs_share admin.mydomain.com(options) # single host
/mnt/nfs_share 192.168.178.0/24(options) # networks
/mnt/nfs_share *.mydomain.com(options) # wildcards
```

### options

#### user id mapping

* `root_squash` # Map requests from uid/gid 0 to the anonymous uid/gid. Note that this does not apply to any other uids or gids that might be equally sensitive, such as user bin or group staff

* `no_root_squash` # By default, NFS translates requests from a root user remotely into a non-privileged user on the server. This was intended as security feature to prevent a root account on the client from using the file system of the host as root. no_root_squash disables this behavior for certain shares. 

  ```sh
  sudo cp <file> <nfs-destination> 
  # führt zu *permission denied*, wenn auf dem Server nicht explizit `no_root_squash` gesetzt ist.
  ```

* `all_squash` # map all uids and gids to the anonymous user; useful for NFS-exported public FTP directories, news spool directories, etc.

* `no_all_squash` # default

* `anonuid=<uid>`|`anongid=<gid>` # explicitly sets the uid and gid of the anonymous account; useful for PC/NFS clients, where you might want all requests appear to be from one user.

#### general options

```sh
/mnt/nfs_share 192.168.43.0/24(rw,sync,no_subtree_check)
```

* `rw` # Allow both read and write requests on this NFS volume. The default is to disallow any request which changes the filesystem. This can also be made explicit by using the `ro` option.
* `sync` # Reply to requests only after the changes have been committed to stable storage
* `no_subtree_check` # This option disables subtree checking, which has mild security implications, but can improve reliability in some circumstances. If a subdirectory of a filesystem is exported, but the whole filesystem isn't then whenever a NFS request arrives, the server must check not only that the accessed file is in the appropriate filesystem (which is easy) but also that it is in the exported tree (which is harder). This check is called the subtree_check. 
  * As a general guide, a *home directory* filesystem, which is normally exported at the root and may see lots of file renames, should be exported with subtree checking disabled.
  * A filesystem which is mostly readonly, and at least doesn't see many file renames (e.g. /usr or /var) and for which subdirectories may be exported, should probably be exported with subtree checks enabled.
  * From release 1.1.0 of nfs-utils onwards, the default will be no_subtree_check as subtree_checking tends to cause more problems  than it is worth.

### examples

```sh
/ 			admin-host(rw,no_root_squash) # per default wird root von NFS als User nobody behandelt; *no_root_squash* deaktiviert dieses Verhalten
/public    	*(ro,all_squash) # jeder darf lesend zugreifen; wird dabei jedoch als User nobody behandelt ( all_squash)
/geheim    	agent*.mydomain.com(rw) # alle Hosts deren Name mit agent beginnt und die Mitglied der Domäne mydomain.com sind…
/home/joe  	pc001(rw,all_squash,anonuid=150,anongid=100) # maps all requests to uid 150 (which is supposedly that of user joe).
/          	master(rw) trusty(rw,no_root_squash) # exports the entire filesystem to machines master and trusty
```


commands
-------------------

### exportfs

maintains the current table of exports for the NFS server

* reads: `/etc/exports`         (NFS server export table)
* writes: `/var/lib/nfs/etab`   (master export table)

In the new mode, exportfs does not give any information to the kernel, but provides it only to rpc.mountd through the */var/lib/nfs/etab* file. rpc.mountd then manages kernel requests for information about exports, as needed.

* Normally the master export table is initialized with the contents of /etc/exports by invoking `exportfs -a`
* However, a system administrator can choose to add or delete exports directly using exportfs:

`exportfs [-o options] [client:/path]`

```sh
exportfs -a # all exports listed in /etc/exports and files under /etc/exports.d are added to /var/lib/nfs/etab; the kernel's export table is also updated as needed

exportfs -r # reexports all directories, synchronizing the master export table (etab) with /etc/exports and files under /etc/exports.d/. This option removes entries in /var/lib/nfs/etab which have been deleted from /etc/exports or files under /etc/exports.d, and removes any entries from the kernel export table which are no longer valid.

exportfs -s # displays the current export list suitable for /etc/exports

exportfs -o <option> # specifies a list of export options in the same manner as in exports
```

access control
---------------------

It is impossible to restrict mounting of NFS exported directory based on user name (for a start, **all** mounting happens as root; at the time of mounting there is no  information which user is going to access it). You can only use usual  access control mechanism on NFS server to limit access to files.

* access to the exports: `/etc/exports`
* access to the server: `/etc/hosts.allow`
  * `rpcbind : clients` 
  * `mountd : client` # "use the daemon name *mountd* even if the *rpc.mountd* binary has a different name"


common cmds
-------------

### showmount

show mount information for an NFS server; queries *rpc.mountd* (local or remote)

```sh
showmount [server] # zeigt die IP-Adressen aller per NFS verbundener Clients [auf server]
showmount -a
showmount --all [server] # zeigt die Zuordnung von Clients und gemounteter Verzeichnisse. This info should not be considered reliable. See the notes on rmtab in rpc.mountd
showmount -d
showmount --directories [server] # lists only the directories mounted by some client
showmount -e
showmount --exports [server] # show the NFS server's export list.

showmount -e karl # shows the export list for karl
```

### nfsstat

zeigt NFS Statistiken über NFS-Server/Clients

```sh
nfsstat -s|--server #zeigt nur serverseitige Statistiken
nfsstat -c|--client # zeigt nur clientseitige Statistiken
nfsstat -n|--nfs # zeigt nur NFS-Statistiken
nfsstat -r|--rpc # zeigt nur RPC-Statistiken
nfsstat -m|--mounts # zeigt Statistiken zu den gemounteten Dateisystemen
nfsstat -2|-3|-4 --mounts # zeigt nur Statistiken zur angegeben NFS-Version
```