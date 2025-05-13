# Distributed File Systems

|                   | NFS                                           | SMB                                    | CIFS                                            |
| ----------------- | --------------------------------------------- | -------------------------------------- | ----------------------------------------------- |
| Environment       | Optimized for Unix-like systems               | Optimized for Windows systems          | Optimized and support for older Windows systems |
| Cross-Platform    | Supported                                     | Supported                              | Supported                                       |
| Authentication    | Host-based Authentication and Kerberos        | User-based Authentication and Kerberos | User-based Authentication                       |
| Encryption        | Not enabled by default                        | AES enabled by default                 | Not supported                                   |
| File Locking      | Advisory and Mandatory locking handled by NLM | Opportunistic locking (oplocks)        | Opportunistic locking (oplocks)                 |
| Network Resources | File sharing and Network Block Devices (NBD)  | File sharing and print sharing         | File sharing and print sharing                  |

# SAMBA

implementiert **SMB** in Linux; ermöglicht somit **Windows-Funktionen** wie die Datei- und Druckdienste unter anderen Betriebssystemen zu nutzen und die Rolle eines Domain Controllers anzunehmen

**Der Pfad zu Freigaben setzt sich folgendermaßen zusammen**: \\*Rechnername*\*Freigabename*

## SMB

**Server Message Block**; Filesharing-Protokoll, das ursprünglich von IBM entwickelt wurde und das es bereits seit den Achtzigern gibt.

Verzeichnisse auf den Remote Hosts, die über den SMB zur Verfügung gestellt werden, werden *Shares* (Freigaben) genannt.

## CIFS

**Common Internet File System**; Implementierung/Dialekt von SMB; Entwickler: Microsoft; heute wird i.d.R. jedoch SMB 2 oder SMB 3 verwendet (*erhebliche* Upgrades gegenüber CIFS)

**NetBIOS**: der hauseigene Namensauflösungsmechanismus von Microsoft

### installation

**[Debian]**

Paket: cifs-utils provides utilities for managing mounts of CIFS network file systems 

Paket: samba provides the components necessary to use Samba as a stand-alone file and print server or as an NT4 or Active Directory domain controller. For use in an NT4 domain or Active Directory realm, you will also need the winbind package. This package is not required for connecting to existing SMB/CIFS servers (see smbclient) or for mounting remote filesystems (see cifs-utils).

### components

- **smbd**: Bereitstellung von Datei- und Druckfreigaben; Authentifizierung von Benutzern
- **nmbd**: Namensauflösung über NetBIOS; Dienstankündigungen; ermöglicht Windows Benutzern über die Netzwerkumgebung Ressourcen aufzufinden; auch als Ersatz für einen WINS-Server in einem Windows-Umfeld verwendbar
- **winbindd**: a daemon which integrates authentication and directory service (user/group lookup) mechanisms from a Windows domain on a Linux system; Winbind based user/group lookups via /etc/nsswitch.conf can be enabled via the libnss-winbind package. Winbind based Windows domain authentication can be enabled via the libpam-winbind package. Authentifizierungsmodule für PAM; muss extra installiert werden: apt install winbind

mit wbinfo kann man eine eine Liste von Benutzern vom Verzeichnisdienst beziehen

# CONFIG

```sh
# Samba wird grundsätzlich über eine einzige Konfigurationsdatei verwaltet
sudo nano /etc/samba/smb.conf # oder
sudo nano /etc/smb/smb.conf
```

Samba unterscheidet nicht zwischen Druckern und Dateisystemen

```sh
man smb.conf
```

### options

(G) indicates that a parameter is specific to the [global] section

(S) indicates that a parameter can be specified in a [service] section

[**global**]

**workgroup** = # (G) Windows Arbeitsgruppe

**browseable** = # (S) kontrolliert ob der Share in der Windows Netzwerkumgebung erscheint

**# guest**

**guest** **account** = nobody # (G) unter dieser UserID findet der Dateizugriff statt, wenn ein nicht authorisierter User (ohne PW) auf einen share zugreift (Voraussetzung: *guest ok = yes*)

**map to guest** = # (G) tells smbd what to do with user login requests that don't match a valid UNIX user

Never # user login requests with an invalid password are rejected; default

Bad User # user logins with an invalid password are rejected, unless the username does not exist, in which case it is treated as a guest login and mapped into the guest account

**guest ok** = yes # (S) no password is required to connect to the service; access via *guest account*; alternativ: *public = yes*

**# security**

**security** = # (G)

user # aktiviert die Authentifizierung via Benutzeraccount; = default if server role is not specified

domain # in this mode Samba will try to validate the username/password by passing it to a Windows NT Domain Controller, in exactly the same way that a Windows NT Server would do; from the client's point of view this is is the same as *security = user*; it only affects how the server deals with the authentication; will only work correctly if net has been used to add this machine into a Windows NT Domain; expects *encrypted passwords = yes*

ads # In this mode, Samba will act as a domain member in an ADS realm. To operate in this mode, the machine running Samba will need to have Kerberos installed and configured and Samba will need to be joined to the ADS realm using the net utility

**encrypt passwords** = # (G) controls whether encrypted passwords will be negotiated with the client; In order for encrypted passwords to work correctly smbd(8) must either have access to a local smbpasswd file or set security = [domain|ads] which causes smbd to authenticate against another server

**hide files** = /*.tmp/*.bak/*.old # (S) Dateien mit den Erweiterungen tmp, bak und old werden nicht angezeigt; wenn ein Benutzer eine solche Datei jedoch direkt aufruft, wird sie geöffnet

**veto files** = /*.tmp/*.bak/*.old # (G) verhindert den Zugriff auf Dateien dieser Endung

​	**valid users** = # (S) if empty (default) then any user can login

​	**# file permissions**

**read only** = yes # (S) es darf nur lesend zugegriffen werden; default = yes

**writeable** = yes # (S) default = no

**write list** = # (S) a list of users that are given read-write access to a service; if the connecting user is in this list then he will get write access, no matter what the *read only* option is set to

**read list** = # (S) a list of users that are given read-only access to a service; if the connecting user is in this list then he will not get write access, no matter what the *read only* option is set to

**force user** = # (S) alle Daten, die über Samba im FS gespeichert werden gehören diesem Benutzer

​	**force group** = # (S) … und dieser Gruppe

​	**force create mode** = # (S) Dateien werden mit mind. diesen Rechten angelegt

​	**create mask** = # (S) Dateien werden max. mit diesen Rechten angelegt

​	**directory mask** = # (S) Verzeichnisse werden max. mit diesen Rechten angelegt

**# limits**

**max connections** = # (S) limits the number of simultaneous connections to a service; connections will be refused if this number of connections to the service are already open.

**lock directory** = *path* # (G) specifies the directory where lock files will be placed. The lock files are used to implement the *max connections* option

**# logging**

logging = file|syslog|systemd[:*option*][@*loglevel*] # (G) configures logging backends

log file = *path* # (G) overrides the name of the Samba log file

**# printers**

printable = yes

printer name = *printer_name*

### sections

#### [global]

\# Parameters in this section apply to the server as a whole, or are defaults for sections that do not specifically define certain items

#### [printers]

\# if this section is included in the configuration file, users are able to connect to any printer specified in the local host's printcap file

\# Standardeintrag, der alle an den Rechner angeschlossene Drucker gegenüber Windows-Computern darstellt; sorgt nur dafür, dass in einer Windows-Netzwerkumgebung der Knoten *Drucker und Fax-Geräte* zu sehen ist; die einzelnen Drucker benötigen immer noch eine eigene Service-Sektion z.B. [lp]

comment = all printers

path = /var/tmp

create mask = 0600

printable = yes # clients may open, write to and submit spool files on the directory specified for the service

browsable = yes

#### [home]

if this section is included in the configuration file, services connecting clients to their home directories can be created on the fly by the server

#### [*service*]

\# Name der Freigabe

​	path = # (S) Verzeichnis das freigegeben werden soll

### users

/etc/samba/smbusers Zuordnung von Windows-Benutzerkonten zu Linux-Benutzerkonten; um diese Authentifizierung via Benutzeraccount zu aktivieren, muss in /etc/samba/smb.conf die Option security = User gesetzt sein

*Linux-Account* = *Windows-Account*

### active-directory

- ​	/etc/samba/smb.conf

[global]

workgroup = *active-directory-workgroup*

netbios name = *samba-server-name*

security = ADS

password server = *domain-controller-name*

realm = *active-directory-domain*

- ​	**DNS**: die AD-Domäne, der ein Samba-Server beitreten soll, muss über DNS auflösbar sein;  

resolv.conf des Samba-Servers: 

**domain** *active-directory-domain*

**search** *active-directory-domain* # so sind auch Hostnamen, bei denen der Domain-Suffix nicht angegeben wurde, auffindbar 

**nameserver** *IP-AD-NS* # der für die AD-Domäne autoritative DNS-Server

lokale Namensauflösung: /etc/hosts

IPv4/6			FQDN					Alias

*ip-samba-server	samba-server.active-directory-domain	samba-server*

- ​	**winbind**: muss konfiguriert werden, damit der Samba-Server Zugriff auf die Benutzer- und Gruppenkonten der AD-Domäne hat: /etc/nsswitch.conf

passwd:	files windbind

shadow:	files windbind

group:	fiiles windbind

# smbcontrol

**send messages to smbd, winbindd, nmbd**

```sh
smbcontrol [smbd|winbindd|nmbd|all] <message-type> <parameter>

smbcontrol smbd reload-config # force daemon to reload smb.conf configuration file; sent to smbd, nmbd, or winbindd
smbcontrol smbd reload-printers # force smbd to reload printers
smbcontrol smbd close-share # order smbd to close the client connections to the named share
```

# samba-tool

**main Samba administration tool**

```sh
samba-tool computer list # lists all computers
```

# smbpasswd

**change a user's SMB password**; die Änderungen selbst werden in `/etc/samba/smbpasswd` vorgenommen

```sh
smbpasswd -a # specifies that the username following should be added to the local smbpasswd file; is ignored if the username following already exists in the smbpasswd file and it is treated like a regular change password command.
```

### smbstatus

**report on current Samba connections**

# mount

* https://wiki.ubuntuusers.de/mount.cifs/

```sh
mount -t smbfs //little_foot/home/music /media/little_foot/music
```

i.d.R. muss der Zugriff auf einen SMB-Share authentifiziert werden:

```sh
# fstab
//192.168.1.2/SambaTest    /home/pi/shares/test    cifs    defaults,noauto,nofail,username=pi,passwd=raspberry    0    0
```

### mount options

- `guest`: Zugriff auf die Freigabe als Gast. Die Angabe eines Passwortes ist hier nicht erforderlich.
- `username` und `password`: Auf manche Freigaben kann nur mittels Benutzername und Passwort zugegriffen werden. Siehe dazu den nachfolgende Abschnitt Benutzer und Passwort. 
- `uid` / `gid`: Mit den Optionen `uid` (Benutzer) bzw. `gid` (Gruppe) wird der Eigentümer aller Dateien gesetzt, die eingehangen werden. 
- `vers`: Erzwingen einer bestimmten SMB-Protokollversion, z. B. `vers=1.0`.
- `noserverino`: Mit `noserverino` werden keine inodes des Servers verwendet, sondern nur die inodes des  Clients. Diese Option kann im Fehlerfall verwendet werden.
- `cache`: Die Deaktivierung des Caches `cache=none` kann helfen, falls die SMB-Freigaben nicht korrekt eingehangen werden.
- `nobrl`: Deaktivierung der Bytebereich-Sperrung (Byte-Range Lock). Einige  Programme kommen mit dem Byte-Range Lock nicht zurecht und können  deshalb auf gemountete SMB-Freigaben trotz korrekter Berechtigungen  nicht schreiben. Abhilfe schafft hier die Mount-Option `nobrl`.

# testparm

**check an** **smb.conf** **configuration file for internal correctness**

# nmblookup

NetBIOS over TCP/IP client used to lookup NetBIOS names/ip-addresses

nmblookup *host*

nmblookup -**S** bzw. --**status** once the name query has returned an IP address then do a node status query as well. A node status query returns the NetBIOS names registered by a host

# net

**tool for administration of Samba and remote CIFS servers**

**net command**

**-U** bzw. **user** *user* user name to use

#### [destination]

-**I** bzw. --**ipaddress** *ip* Adresse des Zielservers

-**S** bzw. --**server** *server* Name des Zielservers

#### [commands]

net **ads** run functions using ADS transport; Info: net help ads

**join** join the local machine to ADS realm

net **status** sessions show list of open sessions

net **status** shares show list of open shares

# SWAT

**Samba Web Administration Tool**; xinetd kann/sollte für SWAT auf TCP-Port 901 lauschen