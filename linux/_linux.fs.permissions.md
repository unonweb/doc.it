# OVERVIEW

| 0    | - - - | no access                             |
| ---- | ----- | ------------------------------------- |
| 1    | `--x` | execute only                          |
| 2    | `-w-` | write access only                     |
| 3    | `-wx` | write and execute                     |
| 4    | `r--` | read only                             |
| 5    | `r-x` | read and execute                      |
| 6    | `rw-` | read and write                        |
| 7    | `rwx` | read, write and execute (full access) |

| symbolisch   | chmod | type | Verwendung                                                   |
| ------------ | ----- | ---- | ------------------------------------------------------------ |
| `rw-r--r--`  | 644   | file | File, readable by everyone but can only be modified by the owner |
| `-rw-rw-rw-` | 666   |      |                                                              |
| `-rw-------` | 600   | file | File can only be accessed by the owner, inaccessible to everyone else |
| `rwx------`  | 700   | file | administrative Aufgaben                                      |
| `rwxr-x---`  | 750   | file |                                                              |
| `rwxr-x--x`  | 751   | dir  | log directory                                                |
| `rwxr-xr-x`  | 755   | dir  | directory, accessible by everyone but only writable by the owner; web store folder |
| `rwxrwx---`  | 770   |      |                                                              |
| `rwxrwxrwx`  | 777   | dir  | directories with proper permissions on files in directory, use this one very carefully |
| `rwxrwxrwx`  | 777   | file | file is readable (and executable) by anybody                 |

If a fourth digit is present, the leftmost digit addresses three additional attributes:

* `setuid` bit
* `setgid` bit 
* `sticky` bit

# FILES

* The executable bit (unlike the rest) on nonsetuid and nonsetguid files isn't much of a security mechanism.
* **Anything you can read, you can run indirectly, and anything you can run, you can read indirectly **
* It's more of a convenience thing: Let the system run it for me directly if the the bit is set, otherwise I need to do it indirectly (`bash script.sh` or some hacking to get the memory image of a no-read-permission executable).
* Unix uses the executable bit to distinguish between programs and data.

## setuid

- SUID-Bit, Set-UID
- ***only files (executables)***
- run as owner UID, not as executor UID >> the UID of the process will be the same of that of the file
- use case: enable a normal user to run a process that needs root privileges
- if an executable is owned by root and launched by a normal user, it will run with root privileges:  example: `passwd`

#### security issues

A process running as *setuid* obtains far greater permissions than it actually needs. *Ping* needs to open a raw network socket, and root rights conferred through setuid allow it to do that, but they also give ping many other powers which it doesn’t need. If the ping binary were overwritten by a hacked version, for example, malicious code could run with full superuser privileges.

#### binaries that need SUID

```sh
/usr/bin/sudo # -rwsr-xr-x
/usr/bin/passwd #
/usr/bin/gpasswd #
/bin/su #
/bin/umount #
/bin/mount #
/bin/fusermount #
/usr/bin/chsh #
/usr/bin/chfn #
/usr/bin/newgrp #
/usr/bin/pkexec #
/bin/ping # 
```

```sh
sudo chmod u-s /bin/ping # removes the SUID-Bit
setcap cap_net_raw+ep /bin/ping # adds a cabability to allow ping to create raw sockets
strace -e socket ping google.com # check if ping
# the ping utility require root privileges in order to open a network socket but it needs to be executed by standard users as well to verify connectivity with other hosts.
```
# DIRECTORIES

- The **read bit** (`r`): read this list; list files within the directory (`ls`)
- The **write bit** (`w`): modify this list i.e. you can {add,rename,delete} names on it, and modify the directory's attributes. 
  - But! You can actually do it only if the execute bit is set too.
- The **execute bit** (`x`): enter the directory (`cd` into it). You need this permission if you want to:
  - access (read, write, execute) items living within.
  - modify the list itself i.e. add, rename, delete names on it (of course the write bit must be set on the directory).
- The **sticky bit** (`T`, or `t`  if the execute bit is set for others): states that files and directories within that directory may only be deleted or renamed by their owner (or  root)

**Interesting case 1**: If you have write + execute  permissions on a directory, you can {delete,rename} items living within  even if you don't have write permission on those items. (use sticky bit  to prevent this)

**Interesting case 2**: If you have execute (but not  write) permission on a directory AND you have write permission on a file living within, you cannot delete the file (because it involves removing it from the list). However, you can erase its contents e.g. if it's a  text file you can use vi to open it and delete everything. The file will still be there, but it will be empty.

## setgid

The GID of the directory, will be passed to the the files created inside 
(die Gruppe wird an Unterordner und Dateien vererbt, die in diesem Ordner neu erstellt werden)

```sh
sudo chmod g+s /home/payload
```

## Sticky Bit

* symbolic: t (T)
* ***only directories***
* Dateien oder Unterordner können nur noch vom Besitzer des Ordners, vom Besitzer einer Datei oder von root gelöscht oder umbenannt werden NICHT von anderen angehörigen der Gruppe!
* Anwendung: Arbeitsgruppenverzeichnisse; /tmp

# EXEC PERMISSIONS

files vs. directories

```sh
touch file
stat file # 0664/-rw-rw-r--

mkdir dir
stat dir # 0775/drwxrwxr-x
```

* files are created with `rw-`
* directories are created with `rwx`
* **Die Ausführbarkeit einer Datei kann nur positiv gewährt** **werden**.

`umask` geht daher bei Verzeichnissen von **0777**, bei Dateien von **0666** aus. Somit kann durch eine umask zwar (versehentlich) der Zugriff auf einen Ordner gewährt werden, die Ausführbarkeit von Dateien bleibt jedoch unangetastet.

## special exec permissions

- With **setuid** we can run things ***as the user who created them***.

- With **setgid** we can run things ***as if we where in the group*** of who created them.

- Wenn das entsprechende **x** "darunter" gesetzt ist, wird ein Kleinbuchstabe verwendet, ansonsten ein Großbuchstabe:

  **T** = Sticky-Bit ohne darunter liegendes Ausführbarkeitsbit

  **t** = Sticky-Bit mit darunter liegendem Ausführbarkeitsbit

|       | special permission | ausgeschrieben    |                                          |
| ----- | ------------------ | ----------------- | ---------------------------------------- |
| user  | `rws` bzw. `rwS`   | `rwxs` bzw. `rws` | SUID anstelle des **x** für Besitzer     |
| group | `rws` bzw. `rwS`   | `rwxs` bzw. `rws` | SGID-Bit anstelle des **x** für Gruppe   |
| other | `rwt` bzw. `rwT`   | `rwxs` bzw. `rws` | Sticky-Bit anstelle des **x** für andere |

### SETGID

* SGID-Bit, Set-GID

* ***files and directories***

* files: run as owner GID, not as executor GID >> the GID of the process will be the same of that of the file

* directories: the GID of the directory, will be passed to the the files created inside

  (die Gruppe wird an Unterordner und Dateien vererbt, die in diesem Ordner neu erstellt werden)

* Anwendung: Arbeitsgruppenverzeichnisse

```sh
sudo chmod g+s /home/payload
```

# UMASK

```sh
umask #0002 
umask -S # u=rwx,g=rwx,o=rx 
umask -p # umask 0002 
umask -S -p # umask -S u=rwx,g=rwx,o=rx 
```

Mit der Option `-p` erhält man eine Ausgabe, die so direkt als Befehl verwendet werden könnte, um die `UMASK` wieder auf den momentan geltenden Wert zu setzen. Wenn man den Wert verändern möchte, könnte man eine solche Ausgaben in einer Variablen speichern, um den Wert später bequem wieder auf den ursprünglichen Wert zurück setzen zu können.

## chmod vs. umask

- **umask** setzt die Rechte *negativ*, d.h. es werden, von 0777 (Ordner) bzw. 0666 (Dateien) ausgehend, Rechte entfernt
- **chmod** setzt die Rechte *positiv*; d.h. es werden, von 0000 ausgehend, Rechte hinzugefügt

