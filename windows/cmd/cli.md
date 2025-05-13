---
title: Windows CLI
tags: ["cmd"]
---

### dll
* `optionalfeatures` # Windows Features aktivieren /deaktivieren   
* `osk` # OnScreen Tastatur 
* `charmap` # Zeichentabelle 
* `calc` # Taschenrechner 


Common
==========

`set` # show environment variables  
`runas` # Programme als ein anderer Benutzer starten  
`wmic` # (Windows Management Instrumentation Commandline) Tool mit dem die Klassen der WMI-Bibliothek ohne Programmierkenntnisse einfach in der Command Line ausgenützt werden können.  

WMIC
---------

`wmic <class> get <property>`

* `/ value`
  * `wmic ComputerSystem get * / value`
  * `wmic ComputerSystem get "TotalPhysicalMemory" / value`
  * `wmic CPU get "Name","CurrentClockSpeed","NumberOfLogicalProcessors" / value` # Kein Leerzeichen!
* `/ every:<n>`
  * `wmic CPU get "LoadPercentage" /every:1`
* `/node:<ip|hostname>`
  * `wmic /node:EVO5016 cpu get "LoadPercentage" /every:1`

`wmic Printer GET Name`
`wmic Printer WHERE "Name like '%' " GET Name`

Dateisystem
------------
  
`dir`    
`mkdir`   # make directory  
`rmdir`   # remove directory    
`attrib`  # Zeigt die Dateiattribute an 
`tree`    # Ordnerstruktur als Baum anzeigen

#### robocopy

`/MIR`    # Spiegelt eine Verzeichnisstruktur

#### fsutil
`fsutil`  # 

`fsutil file createnew <filename> <number_of_bytes>` # create a file

#### ftype
Zeigt die Dateitypen an, die bei den Zuordnungen für die entsprechenden Dateierweiterungen verwendet werden bzw. ändert sie.

`ftype` # zeigt alle aktuellen Dateitypen an, für die Öffnen-Befehle definiert sind.


Netzwerk
-----------

### Commands

#### route
`route print` #  

#### net
ein umfangreiches Netzwerk-Programm mit vielen Unterfunktionen.  
primarily used to manage network resources

* `net /?`      # zeigt eine Liste der verfügbaren net-Befehle an.
* `net config server|workstation` # Display NetBIOS configuration information 
* `net file`    # List files opened by remote computers  
* `net use`     # information about currently mapped drives and devices
* `net user <user>` # lists all user accounts, active or not, on the local computer  
  * `net user <user> /random` # ein (neues) Zufalls-Kennwort vergeben. Achtung: Das neue Kennwort wirkt sofort! 
* `net session` # sehen, wer am Server arbeitet.    
* `net view`    # Display list of domains, computers, or resources shared by specified computers    
  * `net view \\<computer> /all` # Zeigt eine Liste der freigegebenen Ressourcen auf `<computer>`  
* `net share`   # ohne Parameter zeigt alle freigegebenen Ressourcen auf dem lokalen Computer an.   
* `net statistics workstation`
* `net start` # a list of currently running services

#### netsh

* `netsh interface tcp show global`
* `netsh interface tcp set global autotuninglevel=disabled`

#### netstat
`netstat -i` # help/info  
`netstat -ano` # gibt eine Liste aller Netzwerkverbindungen aus.    
`netstat -a` # Zeigt alle Verbindungen und lauschenden Ports an.   
`netstat -o` # Fügt jeder Verbindung die jeweilige Prozess-ID hinzu   
`netstat -f` # Zeigt vollqualifizierte Domänennamen für Remoteadressen an.   
`netstat <sec>` # Zeigt die gewählte Statistik nach \<sec\> erneut an   
`netstat -s` # Statistik für die einzelnen Protokolle    
`netstat -e` # Schnittstellenstatistik   
`netstat -an |findstr /I "abhören|listening"` # list all listening ports  
`netstat -an |findstr /I "hergestellt"` # list all established connections   

#### nbtstat
Zeigt die Protokollstatistik und die aktuellen TCP/IP-Verbindungen an, die NBT (NetBIOS über TCP/IP) verwendet. 
`nbtstat -n`    # Zeigt lokale NetBIOS-Namen an.  
`nbtstat -a`    # RemoteName  
`nbtstat -A`    # IPAddress  
`nbtstat -s`    # lists the current NetBIOS sessions and their status, including statistics  
`nbtstat -S`    # lists sessions table with the destination IP addresses.  

#### ipconfig
`ipconfig /release` #   
`ipconfig /renew` # 
`ipconfig /all` #

#### tracert
`tracert /?` # Hilfe  
`tracert <domain>` #  

#### hostname
`hostname` # show hostname

#### nslookup
`nslookup` # [macOS, Windows und Unix] IP-Adressen oder Domains eines bestimmten Computers mittels DNS ermitteln.  

#### arp

`arp -a <ip>` # Zeigt aktuelle ARP-Einträge für *ip*.

### Beispiele

#### Aus der IP-Adresse die MAC-Adresse ermitteln:

1) `ping <ip-address>` # damit der Client die MAC-Adresse in den ARP-Cache einträgt.
2) `arp -a` # ARP-Cache abfragen


Prozesse
-----------

#### tasklist
* the Linux ps command equivalent
`tasklist`            # zeigt eine Liste der Prozesse an, die zurzeit auf einem lokalen bzw. Remotecomputer ausgeführt werden.  
`tasklist /svc`       # List the services running under each process
`tasklist /FI "imagename eq svchost.exe" /svc` # List the services running under each SvcHost process

#### taskkill
`taskkill`            # Prozesse beenden  
`taskkill /PID <PID>` # Gibt die ID des Prozesses an, der beendet werden soll.

#### pslist
List all processes running on \\workstation64:
`SC \\workstation64 start RemoteRegistry`
`pslist \\workstation64`


User/Groups
--------------

#### whoami

`whoami`          # Zeigt die aktuelle Domäne und den Benutzernamen an  
`whoami /all`     # Zeigt den aktuellen Benutzernamen, Gruppenmitgliedschaften mit den Sicherheitskennungen (SID) und Berechtigungen für das aktuelle Benutzerzugriffstoken an.
`whoami /groups`  # Zeigt die Gruppenmitgliedschaft für den aktuellen Benutzer, die Sicherheitskennungen (SID) und Attribute an.


Utility
-------------

### findstr
`findstr`       # Sucht nach Zeichenfolgen in Dateien.  
`/B`            # Sucht am Zeilenanfang.  
`/E`            # Sucht am Zeilenende.  
`/L`            # Sucht nach dem Text buchstabengetreu.
`/R`            # Sucht nach der Zeichenfolge als regulärem Ausdruck.
`/I`            # Ignoriert Groß-/Kleinschreibung.
`/X`            # Gibt Zeilen aus, die vollkommen übereinstimmen.
`/V`            # Gibt Zeilen aus, die die Zeichenfolge nicht enthalten.
`/N`            # Gibt die Zeilennummer vor jeder Trefferzeile an.
`/M`            # Gibt nur die Namen der Dateien aus, die die Zeichenfolge enthalten.

#### examples
`tasklist | findstr cmd` #  


### msg
Eine Nachricht an einen Benutzer senden.

### sc

**`sc`** (Powershell: `Set-Content`) ist ein Befehlszeilenprogramm für die Kommunikation mit dem Dienststeuerungs-Manager und mit Diensten.

* `sc start <service>`      # Startet einen Dienst.  
* `sc config`               # Ändert die Konfiguration eines Diensts (beständig).  
* `sc query <service>`  
  * sc query windefend      # checks if Windows Defender is running

**`sc [server] [cmd] [service]`**
* `<servername>` # Gibt den Namen des Remote Servers an, auf dem sich der Dienst befindet. Der Name muss das Universal Naming Convention (UNC)-Format verwenden

### type
**`type <file>`** # Zeigt den Inhalt einer oder mehrerer Textdateien an.


Systeminfo
------------

`systeminfo`  #  
`winver`      # prints Windows 10 Version number  
`ver`         # Zeigt die Windows-Version an.


System
------------

`shutdown -i` # Remotecomputer herunterfahren  
`shutdown.exe /r /o /t 00` # neustart im abgesicherten Modus  

`schtasks` # geplante Aufgaben lokal oder remote erstellen, abfragen, löschen, ändern, ausführen und beenden.

Gruppenrichtlinien
------------------------

`gpresult`  # Gruppenrichtlinien anzeigen
`gpupdate`  #	Gruppenrichtlinien aktualisieren 

Ausführen... / Run...
=====================

### shell: ...
* `shell:startup` # Autostart-Ordner  
* `downloads` #    
* `documents` #  
* `shell:ConnectionsFolder` # Systemsteuerung\Alle Systemsteuerungselemente\Netzwerkverbindungen

PS H:\> `$k = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions"`  
PS H:\> `dir $k | gpv -Name Name | sort`   


CMD
==========

### Pipes

Befehl | Befehl

### Redirects

* `cmd > file` (Ausgabe in Datei)
* `cmd < file` (Eingabe aus Datei) 

### Operatoren

* `cmdA & cmdB` (Der zweite Befehl wird direkt nach dem ersten ausgeführt.)
* `cmdA && cmdB` (Der zweite Befehl wird nur ausgeführt, wenn der erste erfolgreich war.)
* `cmdA || cmdB` (Der zweite Befehl wird nur ausgeführt, wenn der erste nicht erfolgreich war.)
* `cmdA && cmdB || cmdC` (Der zweite Befehl wird nur dann ausgeführt, wenn der erste Befehl erfolgreich war, ansonsten wird der dritte Befehl ausgeführt.)

### Kommandos

`color <background><foreground>` # no space  
`color 02` # black green

Batch
-------------

Mit Batch Dateien können einfache Abläufe in Microsoft Windows automatisiert werden. Batch Dateien verwenden die Befehle der Eingabeaufforderung, im einfachsten Fall können diese cmd-Befehle in einer einfachen Textdatei Zeile für Zeile eingetragen werden, es wird dann ein Befehl nach dem anderen ausgeführt.

Windows Batch stammen aus dem MS-DOS Zeitalter. Zu dieser Zeit hatten Batch Dateien die Dateiendung **.bat**. Seit Windows 2000 wird die Endung **.cmd** verwendet. Als Nachfolger von Windows Batch gilt die Microsoft PowerShell.

### Kommandos

**`call`** # Ruft eine Batch-Datei innerhalb einer anderen Batch-Datei auf. Der Befehl hat keine Wirkung, wenn Sie diesen nicht in einer Batch-Datei, sondern direkt in CMD eingeben. 

### Examples

#### Endlosschleife

```batch
  :endlessLoop
  start winver.exe
  goto endlessLoop
```




