---
title: Windows
---

Entwicklung
=============

## COM

**Component Object Model** ist eine von Microsoft entwickelte Technik zur Interprozesskommunikation unter Windows.

## NuGet

von Microsoft unterstützter Mechanismus zur Freigabe von Code;   
#### NuGet-Paket  
* ist eine einzelne ZIP-Datei mit der Erweiterung **.nupkg**  
* enthält kompilierten Code (DLLs), andere Dateien im Zusammenhang mit diesem Code und ein beschreibendes Manifest  
#### teilen
Entwickler, die Code teilen wollen, erstellen und veröffentlichen Pakete auf einem öffentlichen oder privaten Host.  
Da NuGet neben dem öffentlichen Host „nuget.org“ auch private Hosts unterstützt, können Sie NuGet-Pakete verwenden, um Code zu teilen, der ausschließlich für eine Organisation oder eine Arbeitsgruppe zur Verfügung steht.

UWP
--------------

**Universal Windows Platform**; eine Laufzeitumgebung für Anwendungen, die unter Windows 10 ausgeführt werden können.

Die UWP stellt eine Weiterentwicklung der mit Windows 8 eingeführten *Windows Runtime* dar. Basierend auf der UWP ist es möglich, eine Anwendung mit ein und derselben Codebasis für unterschiedliche Klassen von Zielgeräten zu entwickeln und im Windows Store bereitzustellen. Die Zielgeräte können beispielsweise Desktop-Computer, Mobilgeräte, eine Xbox oder IdD-Geräte sein, wie etwa ein Raspberry Pi.


SONSTIGES
========

paths
-------

### Windows 

#### UNC (Universal Naming Convention)
* UNC (**network path**; describes the location of a network resource, such as a shared file, directory, or printer.  
`\\<ComputerName>\<SharedFolder>\<Resource>` #  
`\\<Server>\<Volume>\<File>` #

* LFS (Local File System): `C:\<path>` #

### Linux

Many applications on Unix-like systems (for example, scp, rcp and rsync) use resource definitions such as:  
`hostname:/directorypath/resource`  
or like URLs with the service name (here 'smb'):  
`smb://hostname/directorypath/resource`


DLL
--------

* Insbesondere Funktionalitäten, die von mehreren Programmen benötigt werden, stellt das System bevorzugt in Form von DLLs zur Verfügung.
* Programmcode, der von unterschiedlicher Software genutzt und bei Bedarf dynamisch eingebunden (verlinkt) werden kann. 
* Nutzen: Speicherplatz & Modularisierung (DLLs lassen sich unabhängig von der verwendeten Software anpassen und aktualisieren)


NETZWERK
============================================


Netzlaufwerk
---------------

#### Netzlaufwerk als Admin
Wenn Sie im Explorer ein Netzlaufwerk einbinden, dann gilt das nur für Ihr Benutzerkonto, und auch das nur für die derzeitige Anmeldung. Wenn Sie ein Programm mit Administratorrechten starten, meldet die Benutzerkontensteuerung Sie aber ein zweites Mal an, und zwar diesmal mit erweiterten Rechten. Diese zweite Anmeldung weiß nichts von dem Netzlaufwerk.


SNMP (Simple Network Management Protocol)
----------------------------------

* allowes network monitoring software to remotely gather basic system information from Windows
* It appears SNMP has been deprecated in Windows 10 1809

NetBIOS
-------------

*Network Basic Input Output System* ist eine Programmierschnittstelle (API) zur Kommunikation zwischen zwei Programmen über ein lokales Netzwerk

### Funktionen
* Namensauflösung
* Verbindungsloser Datenaustausch (datagram service)
* Verbindungsorientierter Datenaustausch (session service)


### Anwendung

#### SMB
In TCP/IP-Netzen wurde SMB ursprünglich in NetBIOS over TCP/IP (NBT) über die TCP/UDP-Ports 137–139 gekapselt, die Namensauflösung erfolgte häufig mittels WINS bzw. Broadcasts. Heutige Windows-Versionen nutzen SMB direkt auf dem TCP-Port 445 und lösen Namen per DNS und in kleinen Netzwerken per LLMNR auf. 

#### WINS

WINS is a NetBIOS name to IP database. It’s a flat database with no hierarchal structure – simply one name to one IP. It’s similar to DNS, but DNS is hierarchal.   

When you install WINS and configure all machines to use WINS, then the NetBIOS aware processes and functions will recognize there is a WINS server configured, and instead of broadcasting every 60 seconds, it simply registers its name and related services to the WINS database instead of repeatedly broadcasting, or simply put, it shuts up yelling out its name every 60 seconds. 


### NetBIOS over TCP/IP 
kurz NetBT oder NBT, ist ein Netzwerkprotokoll, das es ermöglicht, auf der Programmierschnittstelle NetBIOS aufbauende Programme über das Netzwerkprotokoll TCP/IP zu verwenden. 

* Port **137** UDP      # NetBIOS-Namensauflösung
* Port **138** UDP      # Datagram Service
* Port **139** TCP      # Session Service


### Kommandos

#### nbtstat
**`nbtstat`**     # a diagnostic tool for NetBIOS over TCP/IP
* `nbtstat -n`    # Zeigt lokale NetBIOS-Namen an.  
  * If you get any response other than "Failed to access NetBT driver -- NetBT may not be loaded" this means NetBIOS is enabled.
* `nbtstat -a`    # RemoteName  
* `nbtstat -A`    # IPAddress  
* `nbtstat -s`    # lists the current NetBIOS sessions and their status, including statistics  
* `nbtstat -S`    # lists sessions table with the destination IP addresses.  

#### check if running
`sc query netbt`  # check if it's running

#### disable NetBIOS

```batch
  net stop "TCP/IP NetBIOS Helper"
  net stop netbt
  
  sc config netbt start= disabled
  sc config lmhosts start= disabled
```

`nbtstat -n`      # Confirm that it's been disabled


SMB
-------

SMB implementiert ein Netzwerkdateisystem ähnlich wie NFS und ist damit vom zugrundeliegenden Dateisystem des Servers größtenteils unabhängig. Höhere Protokolle wie DFS setzen wiederum auf SMB auf. 

In TCP/IP-Netzen wurde SMB ursprünglich in NetBIOS over TCP/IP (NBT) über die TCP/UDP-Ports 137–139 gekapselt, die Namensauflösung erfolgte häufig mittels WINS bzw. Broadcasts. Heutige Windows-Versionen nutzen SMB direkt auf dem TCP-Port 445 und lösen Namen per DNS und in kleinen Netzwerken per LLMNR auf. 

#### Versteckte Windows-Freigaben
Das Programm "fsmgmt.msc" zeigt neben den selbst angelegten auch versteckte Freigaben an. Sie erkennen versteckte Freigaben an dem Dollarzeichen hinter dem Freigabenamen. Solche Freigaben zeigt Windows unter "Netzwerk" nicht an. 
* versteckte Freigaben: *`ADMIN$`*, *`IPC$`* und *`C$`*
* auch administrative Freigaben genannt. 
* Laut Microsoft sollten diese nicht aufgehoben werden, da es sonst zu Fehlern in Windows kommen kann. 

Ports
-------

#### PortQry
* download PortQry.exe
* cd c:\PortQryV2
* 

`%SystemRoot%\system32\drivers\etc\services`

* **`88`**          # **kerberos**
* **`135`**         # **epmap** (endpoint mapper) für **RPCs** (Remote Procedure Calls)  
  * DCE Endpunkt-Auflösung (Distributed Computing Environment)
  * You can bind to that port on a remote computer, anonymously, and either enumerate all the services (endpoints) available on that computer, or you can request what port a specific service is running on if you know what you're looking for.
* **`137-139`**     # **NetBIOS over TCP/IP**
  * `137`/udp       # NetBIOS-Namensauflösung (**WINS**)
  * `138`/udp       # Datagram Service
  * `139`/tcp       # Session Service
  * früher wurde SMB in *NetBIOS over TCP* gekapselt
* **`389`**         # **LDAP**
* **`686`**         # **LDAP-SSL**
* **`445`**         # **SMB** over IP  
  * die gesamte SMB-Kommunikation (Common Internet File System)
  * wird von den Microsoft Directory Services genutzt
  * WannaCry-Infektion erfolgte überwiegend über den TCP-Port 445
  * gemeinsamen Zugriff auf Drucker
  * Microsoft-DS File Sharing
* **`3389`**: **RDP** (TCP/UDP)

### Linux

* **`111`**/tcp     # a port mapper with similar functions to Microsoft's port 135
* **`111`**/udp


Routing table
----------------

`route print`  #

* **order**: In general, the routing table is organized by priority. Windows shows the table upside down: **first entry is the last rule** (i. e. if all others fail, use this one).

**0.0.0.0** #  default route, if no other rule matches use this one.
**on-link** | **Auf Verbindung** # a route that's directly reachable (the NIC is in direct contact with it; on the same subnet); no gateway, no routing

#### example

https://stackoverflow.com/questions/48006173/understanding-windows-routing-tables

Destination|Network Mask|Gateway|Interface|Metric
-----------|------------|-------|---------|-------
0.0.0.0|0.0.0.0|192.168.178.1|192.168.178.28|50
192.168.178.0|255.255.255.0|On-link|192.168.178.28|306
192.168.178.28|255.255.255.255|On-link|192.168.178.28|306
192.168.178.255|255.255.255.255|On-link|192.168.178.28|306
224.0.0.0|240.0.0.0|On-link|192.168.178.28|306
255.255.255.255|255.255.255.255|On-link|192.168.178.28|306


DIENSTE / SERVICES
===================

`services.msc`

Service Control Manager (SCM)
-------------------------------

* Is started at system boot. 
* It is a remote procedure call (RPC) server, so that service configuration and service control programs can manipulate services on remote machines.

* Verwaltet in der Windows-Registrierungsdatenbank (Registry) eine Liste der zu startenden Dienste.
* Nach dem Systemstart führt der SCM für jeden dort eingetragenen Dienst eine Instanz von **svchost.exe** als Prozess aus.

The service functions provide an interface for the following tasks performed by the SCM:

* Maintaining the database of installed services.
* Starting services and driver services either upon system startup or upon demand.
* Enumerating installed services and driver services.
* Maintaining status information for running services and driver services.
* Transmitting control requests to running services.
* Locking and unlocking the service database.


### svchost.exe

* Service-Host  
* \Windows\System32
* Unter Windows 10 haben die svchost.exe-Prozesse die vorangestellte Bezeichnung *Diensthost:*
* wird nach dem Systemstart vom *Service Control Manager* aufgerufen

#### Wofür benötigt man svchost.exe?

Warum ist zum Start von Diensten überhaupt eine zusätzliche Software notwendig? Das hängt mit einer erhöhten Effizienz und spezifischen Konzepten wie den sogenannten *Dynamic Link Libraries* (DLLs) zusammen. Letztere verwendet svchost.exe nämlich, um einen Dienst auszuführen. 

#### Als Dienst anmelden / Log on as a service
determines which service accounts can register a process as a service. Running a process under a service account circumvents the need for human intervention.


WinRM
----------------------

Windows-Remoteverwaltung. Stellt das "WS-Management"-Protokoll für die Remoteverwaltung der Hard- und Software im Netzwerk bereit. Der Dienst ist für die Bearbeitung von Anfragen über das Netzwerk zuständig.  
Wird benötigt für 
* WMI-Abfragen im Netzwerk

RpcSs
----------------------

Einer der wichtigsten Dienste unter Windows. Die meisten anderen Dienste im System sind von diesem Dienst abhängig und können nicht gestartet werden, wenn dieser Dienst nicht läuft. Ein Deaktivieren dieses Dienstes führt dazu, dass mit Windows nicht mehr gearbeitet werden kann und das System auch nicht mehr richtig startet. Dient der Endpunktzuordnung und als COM-Dienststeuerungsverwaltung. Wenn dieser Dienst beendet oder deaktiviert wird, werden Programme, die COM- oder RPC-Dienste verwenden, nicht mehr richtig funktionieren.
* Remoteprozeduraufruf (RPC)


SICHERHEIT
===============


### NetBIOS

* abschalten, falls nicht verwendet


### Turn Windows features on or off
SMB 1.0/CIFS File Sharing Support

stupac 
maraschnow
serverumstellung
verweis messtechnin


DRUCKER
===============

Netzwerkdrucker
----------------

Grundsätzlich gibt es zwei Möglichkeiten wie man einen Netzwerkdrucker einrichten und darüber drucken kann.

1. Drucker lokal anschließen und für das Netzwerk freigeben
2. Drucker direkt mit dem Netzwerk verbinden

### 1. Drucker lokal am PC anschließen und im Netzwerk freigeben
Der Vorteil bei dieser Variante ist, *dass die Treiber für den Drucker vom freigebenden Computer bereitgestellt werden*. Während der Installation sucht man den freigegebenen Drucker im Netzwerk und die notwendigen Treiber werden über das Netzwerk geladen.   
Dabei spielt es keine Rolle, ob der Drucker an einem gewöhnlichen PC oder an einem Druckserver angeschlossen wird. Der einzige Unterschied zwischen einem gewöhnlichen PC und einem Druckserver ist die Tatsache, dass ein Druckserver rund um die Uhr läuft und meistens eine spezielle Software für die Druckerverwaltung installiert ist, wobei dies nicht zwangsläufig notwendig ist. Es ändert jedoch nichts an der Tatsache, dass der Drucker lokal angeschlossen und freigegeben wird.

#### Druckerpool

Hierbei werden mehrere Drucker zu einem Druckerpool zusammengeschlossen. Die Druckaufträge die ankommen, werden automatisch ausbalanciert und gleichmäßig auf die Drucker verteilt. So wird für eine gleichmäßige Auslastung gesorgt.

Einen Druckerpool richtet man auf dem Computer bzw. Druckserver ein, auf dem die Drucker eingerichtet sind. Im Register *Anschlüsse* wählt man alle Drucker aus, die ein Druckerpool bilden sollen und aktiviert den *Druckerpool*.

### 2. Drucker direkt mit dem Netzwerk verbinden
Besitzt der Drucker eine eigene Netzwerkkarte, kann der Drucker direkt mit dem Netzwerk verbunden werden. Diese Variante hat den Vorteil, dass kein anderer Computer oder Druckserver eingeschaltet sein muss, damit man darauf drucken kann. Der Netzwerkdrucker wird als unabhängiges Gerät im Netzwerk behandelt und erhält auch eine eigene IP-Adresse. Der Nachteil ist, dass für die Installation die Treiber nicht automatisch bereitgestellt werden können, sondern manuell auf Datenträgern oder einer Ressource im Netzwerk bereitgestellt werden muss.


Windows & Drucker
----------------------

#### spoolsv.exe
`C:\Windows\System32\spoolsv.exe`
* sorgt dafür, dass sich ausstehende Druckaufträge ordentlich in eine Warteschlange einreihen und im Hintergrund aufs Drucken warten.

`C:\WINDOWS\system32\spool\PRINTERS`

### Windows Drucker Logs

Ereignisanzeige (lokal) --> Anwendungs- und Dienstprotokolle --> Microsoft --> Windows --> PrintService --> Betriebsbereit
Event Viewer (local) --> Applications and Services Logs --> Microsoft --> Windows --> PrintService --> Operational

### Remote Desktop Services

[Remote Desktop Services: Fünf Probleme mit der Druckerumleitung](https://www.computerweekly.com/de/tipp/Remote-Desktop-Services-Fuenf-Probleme-mit-der-Druckerumleitung)

### Line Printer Protocol

* **LPR**: Line Printer Remote
* **LPD**: Line Printer Daemon

* ein Plattform-unabhängiges Druckerprotokoll, das unter TCP/IP läuft
* wurde ursprünglich für die Unix Berkeley Software Distribution (BSD) entwickelt, hat sich aber auch im Desktop-Bereich als De-Facto-Standard eingebürgert.
* ermöglicht den Zugriff auf Drucker, die an Remote-Computer angeschlossen sind


* Port **515**/tcp: A server for the LPD protocol listens here for requests 
* A request begins with a byte containing the request code, followed by the arguments to the request, and is terminated by an ASCII LF character.

An LPD printer is identified by 
* the IP address of the server machine
* the queue name on that machine. Many different queue names may exist in one LPD server, with each queue having unique settings.

A printer that supports LPD/LPR is sometimes referred to as a "TCP/IP printer" (TCP/IP is used to establish connections between printers and clients on a network), although that term would be equally applicable to a printer that supports the Internet Printing Protocol. 

#### lpr vs. raw

LPR bedeutet, dass der Drucker irgendwo im Netz und nicht direkt am PC angeschlossen ist. Die Bezeichnung kommt aus der UNIX-Welt, wo Drucker grundsätzlich über eine Druckwarteschlange (Spooler) angesprochen werden. Daher muss hier auch immer der Name einer Warteschlange (lprX) angegeben werden. Die LPR-Methode fügt den Druckdaten noch weitere Infos wie Zeitpunkt des Druckauftrags, Name des Senders, Größe des Jobs, Trennseite, Seitenvorschub, etc. hinzu.

Der RAW-Modus lässt – wie der Name schon sagt – die Druckdaten vollkommen unverändert und ist daher in einer reinen Windowsumgebung normalerweise die erste Wahl, d.h. so funktioniert es üblicherweise ohne Probleme.


AUFGABENPLANUNG
=================

* **start in** # In welchem Ordner soll das Programm/Skript ausgeführt werden? Diese Angabe ist dann notwendig, wenn im Skript relative Pfade verwendet werden!
* **Programm/Skript**: `%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe`: hier nicht den Namen des Skripts, sondern den Pfad zum Programm PowerShell.exe hinterlegen
* **Argumente hinzufügen**: `-command "C:\meine_Skripts\mytask.ps1"` 

#### Daten mit NAS synchronisieren
* **Programm/Skript**: `robocopy.exe`
* **Argumente hinzufügen**: `D:\Daten \\NAS-Laufwerk\Tausch /MIR`
  * `D:\Daten` # Quellverzeichnis
  * `\\NAS-Laufwerk\Tausch` # die Freigabe des Netzlaufwerks
  * `/MIR` # bewirkt, dass Robocopy das Quellverzeichnis spiegelt.