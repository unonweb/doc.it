---
title: GUI
---

EXE
==============

* `msinfo32.exe` #  
* `msconfig.exe` # 
  * officially called *System Configuration*


Microsoft Management Console (MMC)
======================================


GUI zur Verwaltung von Windows.  

``Windows\System32\`` # default location of .msc files

* administrators may create and assign tools to specific user groups, computer systems, or users

### Snap-Ins: Allgemein
Die MMC selbst führt keine Verwaltungsaufgaben aus, sondern fasst Verwaltungsprogramme zusammen. Ein solches Programm für die MMC wird **Snap-In** genannt und trägt die Dateinamenserweiterung **.msc**.   
 
The management console can host Component Object Model components called snap-ins. Most of Microsoft's administration tools are implemented as MMC snap-ins.

Snap-Ins können direkt über das Startmenü, die Kommandozeile, PowerShell oder den Dialog „Ausführen“ gestartet werden, wodurch die MMC mit dem entsprechenden Modul ausgeführt wird.  
Teilweise sind diese jedoch nur in Verbindung mit einem Windows-Server funktionell beziehungsweise nur auf einem solchen verfügbar.  
 
Bei vielen Snap-Ins ist sowohl möglich, einen lokalen Computer, als auch einen entfernten Computer zu verwalten.  
 
Ferner ist es möglich, über die MMC selbst, beziehungsweise über deren SDK, oder die Win32-API, benutzerdefinierte Ansichten und Konsolen zu erstellen oder zu entwickeln.  

### Snap-Ins: Windows Server

* `servermanager.msc`:  Server Manager
* `wsus.msc`:           *Windows Server Update Services*

#### Active Directory
* `dsa.msc`: *Active Directory Users and Computers* (Benutzer und Computer)  
  Browse, create, and manipulate users, groups, computers, OUs, and other domain specific objects. Create and save queries to find objects, raise the functional level of a domain, view and transfer the FSMOs for a domain, delegate control, and much more.
* `dssite.msc`: *Active Directory Sites and Services* (Standorte und Dienste)
* `domain.msc`: *Active Directory Domains and Trust*

#### DNS
* `dnsmgmt.msc`:        *dns management*

#### DHCP
* `dhcpmgmt.msc`:       dhcp Management

#### Group Policies
* `gpmc.msc`:           *group policy management console* (Gruppenrichtlinienverwaltung)
  * `gpedit.msc`
* `gpme.msc`:           *Group Policy Mangement Editor*

#### Certificates
* `certserv.msc`:       Certification Authority (Zertifizierungsstelle)

#### Virtualization
* `virtmgmt.msc`:       *virtualization management* (Hyper-V Manager)


#### Web Server
* `iis.msc`:            *Internet Information Services Manager*


### Snap-Ins: Windows

* `gpedit.msc`:         Editor für lokale Gruppenrichtlinien (*group policy edit*)
* `wf.msc`:             *Windows Firewall*
* `secpol.msc`:         (Local) *Security Policy* # account policies, public key policies, or advanced audit policy configuration
* `devmoderunasuserconfig.msc`: User Manager # Complete customization of the start menu items and the taskbar
* `certmgr.msc`:        *Certificate Manager*
* `rsop.msc`:           *Resultant Set of Policy*; gather only computer-specific policy information.


#### computer management

`compmgmt.msc`:     *computer management* (Computerverwaltung)
  
* System Tools
  * `taskschd.msc`: *Task Scheduler* (Aufgabenplanung)
  * `eventvwr.msc`: *Event Viewer* (Ereignisanzeige) # displays operating system, software, and hardware events
  * `fsmgmt.msc`:   Shared Folders (Freigegebene Ordner) # Loads the list of shared folders, sessions, and open files
  * `lusrmgr.msc`:  *local user manager* (Local User and Groups)
  * `perfmon.msc`:  *Performance Monitor* (Leistung)
  * `devmgmt.msc`:  *device management* (Geräte-Manager)

* Storage
  * `diskmgmt.msc`: *Disk Management* (Datenträgerverwaltung)
  
* Services and Applications
  * `services.msc`: *Services*
  * `wmimgmt.msc`:  *WMI Management* # Windows Management Instrumentation Service


Control Panel Applets
==========================

`c:\Windows\System32` #   
`ls *.cpl` # list all .cpl files

CPL
---------

* control panel
* `%SystemRoot%\system32`
* inhaltlich nichts anderes als umbenannte DLL-Dateien

#### God mode

`<anything>.{ED7BA470-8E54-465E-825C-99712043E01C}`

### Beispiele
`sysdm.cpl,4` # open the 4th tab (*System Protection*) of the *System Properties* control panel applet

`Access.cpl`     # Accessibility properties  
`Appwiz.cpl`     # Add/Remove Programs properties  
`Desk.cpl`       # Display properties  
`FindFast.cpl`   # FindFast (included with Microsoft Office for Windows 95)  
`Inetcpl.cpl`    # Internet properties  
`Intl.cpl`       # Regional Settings properties  
`Joy.cpl`        # Joystick properties  
`Main.cpl`       # Mouse, Fonts, Keyboard, and Printers properties  
`Mlcfg32.cpl`    # Microsoft Exchange or Windows Messaging properties  
`Mmsys.cpl`      # Multimedia properties  
`Modem.cpl`      # Modem properties  
`Netcpl.cpl`     # Network properties  
`Odbccp32.cpl`   # Data Sources (32-bit ODBC, included w/ Microsoft Office)  
`Password.cpl`   # Password properties  
`Sticpl.cpl`     # Scanners and Cameras properties  
`Sysdm.cpl`      # System properties and Add New Hardware wizard  
`Themes.cpl`     # Desktop Themes   
`TimeDate.cpl`   # Date/Time properties  
`Wgpocpl.cpl`    # Microsoft Mail Post Office  