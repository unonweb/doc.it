---
title: Windows Server
---

# SERVER

## Rollen

### roles / role services

* Active Directory Certificate Services (**AD-Certificate**)  
  *  Certification Authority
* Active Directory Rights Management Services (**ADRMS**)  
  * Active Directory Rights Management Server
* Domain Controler
* DHCP Server 
* DNS Server
* Host Guardian Service
* Hyper-V
* RemoteAccess  
  * DirectAccess and VPN (RAS)
  * Routing
  * Web Application Proxy
* Web Server IIS  
  * Static Content
  * HTTP Redirection
  * Request Filtering
  * URL Authorization
  * Basic Authentication
  * Server Side Includes
  * FTP Server
  * 

* File and Storage Services  
  * File and iSCSI Services
  * File Server
  * Server for NFS
  * Work Folders

## Features

* Gruppenrichtlinienverwaltung

### WINS
**Windows Internet Name Service**; eine Umsetzung des Netzwerkprotokolls NetBIOS over TCP/IP durch Microsoft.  
Anders als der Name vermuten lässt, wird WINS nicht im Internet, sondern in LANs verwendet.

Wie DNS dient WINS der zentralen Namensauflösung. Wenn ein Gerät ans Netz geht, registriert es seinen Namen und seine IP-Adresse automatisch beim WINS-Server. Ändert sich die IP-Adresse oder der Name des Geräts, ist deshalb kein manueller Eingriff nötig, wie er bei Lmhosts- oder Hosts-Dateien anfällt.

Im Unterschied zu DNS kann WINS keine Hierarchien abbilden. Zudem hat WINS ein Problem damit, veraltete Zuordnungen zu löschen. Der Abgleich mehrerer WINS-Server gestaltet sich schwierig. **Microsoft empfiehlt, auf WINS-Server zu verzichten und ausschließlich DNS zur Namensauflösung einzusetzen**, sofern diese nicht bedingt durch den Einsatz älterer Betriebssysteme oder Programme notwendig sind.

## Deployment

* Installation auf vielen Rechnern
* Massenrollouts (automatisierte Installation eines OS auf vielen Computern)

### Windows Server 2016 Eigenschaften

* imagebasiert
* hardwareunabhängiges Imageformat: **WIM** (Windows Image Microsoft)
  * install.wim
  * boot.wim (enthält Windows PE)
* sprachneutral: alle Sprachpakete sind optionale Komponenten
* startet nicht im DOS-Modus, sondern in **Windows PE** (Mini OS)

### DISM: customize image

#### info wimfile
`dism /get-wiminfo /wimfile:<pfad>` # Welche Versionen sind verfügbar  
`dism /get-wiminfo /wimfile:D:\sources\install.wim` # Infos über das Image auf der DVD  
`dism /get-wiminfo /wimfile:<pfad> /index:<index>` # Informationen über eine spezifische Image-Version  

#### mount
Bevor das Image bearbeitet werden kann, muss es gemountet werden.  
Da die Installations-DVD schreibgeschützt ist, müssen die Dateien zuerst auf die Festplatte kopiert werden.  
`dism /mount-wim /wimfile:<path\install.wim> /index:<index> /mountdir:<mountdir>` #  

#### info image
`dism /image:<mountdir> /get-drivers` # Treiberinformationen  
`dism /image:<mountdir> /get-features` # Informationen über Windows-Features  
`dism /image:<mountdir> /get-packages` # Informationen über Feature-Packs, Sprachpakete, Updates, etc.

#### modify image
`dism /image:<mountdir> /disable-feature:<feature>` # disable features  
`dism /image:<mountdir> /add-package:<path>` # add packages  
`dism /image:<mountdir> /add-package:<path>` # add packages  
`dism /image:<mountdir> /add-provisionedAppxPackage:<path>` # Adds an app package (.appx) that will install for each new user to a Windows image.
 
`dism /image:<mountdir> /add-AppxPackage:<path>` # add app for one user

`dism /commit-wim /mountdir:<mountdir>` # save changes  
`dism /unmount-wim /mountdir:<mountdir> /commit` # unmount  

### Clone system

1) Installation des Referenzcomputers mit allen Anwendungen und Einstellungen
2) Ausführen von `SYSPREP` um alle individuellen Merkmale zu entfernen
3) Erstellen eines Images; speichern auf einer Netzwerkfreigabe
4) Verteilen des Images an allen anderen zu installierenden Computer

![clone system](img/clone_system.png)

### Assessment and deployment kit

#### Features
* Bereitstellungstools
* Windows-Vorinstallationsumgebung (Windows-PE)  
*Windows-Kits* >> **Umgebung für Bereitstellungs** 

### Image deployment

* Starten des Client mit der Boot-PE-CD und Zuweisung des Image (ähnlich einer Einzelplatzinstallation)  
* Vollautomatische Bereitstellung durch den Windows-Dienst **WDS** (Windows Deployment Service)  

#### Antwortdatei
Ziel: unbeaufsichtigte Einzelplatzinstallation  
*Windows Kits* >> **Windows System Image Manager** 

### Installationsphasen

1) **WindowsPE**: grundsätzliche OS-Einstellungen (Partitionen, Product-Key)
2) **offlineServicing**: dem Image Updates, Sprachpakete, etc. hinzufügen
3) **generalize**: Einstellungen; nur verfügbar wenn das Image mit `sysprep /generalize` erstellt wurde
4) **specialize**: systemspezifische Konfigurationen; z.B. Netzwerkeinstellungen; Domain Infos
5) **auditSystem**: Zeitspanne kurz bevor der Benutzer sich anmeldet; nur verfügbar wenn das Image mit `sysprep /audit` erstellt wurde
6) **auditUser**: Einstellungen 
7) **oobeSystem**: Einstellungen, die kurz vor dem Willkommensbildschirm relevant sind

## Domain Controler

Auf einem DC gibt es keine lokalen Konten. Die einzige Datenbank, die auf einem DC läuft, ist das Active-Directory. Das heißt, man meldet sich immer mit einem Konto aus dem AD an. Einzig das Passwort für den DSRM (Directory Services Restore Mode) ist nicht im AD gespeichert.

## File server

Freigaben für Pfade  
Zugriffsberechtigungen für Benutzer(gruppen) (Active Directory)

## BackOffice
... haben eine **Clientkomponente** und eine **Serverkomponente**
* Exchange Server (Client/Frontend: Outlook || Server/Backend: Postfächer)
* SQL Server

## Hyper-V Server

## CORE SERVER

`sconfig` # minimale Konfigurationsoberfläche
1) Domäne/Arbeitsgruppe
2) Computername
3) Lokaler Administrator
4) Remoteverwaltung

## WEB SERVER

IIS  
früher: Internet Information Server  
heute: Internet Information Service (soll verdeutlichen, dass es sich nicht um ein eigenständiges Produkt, sondern einen im Microsoft Betriebssystem integrierten Dienst handelt)  

Der Webserver ist sowohl in den Server- als auch in den Client-Betriebssystemen nutzbar.  

Per IIS lassen sich Informationen wie Dateien oder Dokumente im Netz bereitstellen. Unterstützte Kommunikationsprotokolle sind HTTP, HTTPS, FTP, SMTP, POP3 oder WebDAV und weitere Protokolle. Der Webserver ist in der Lage, ASP.NET-Applikationen auszuführen. Er konkurriert mit dem Apache Webserver und stellt eine Alternative zu so genannten LAMP-basierten (Linux, Apache, MySQL, PHP) Systemen dar.

#### WIMA
Serverseitig arbeitet das Microsoft Webframework **ASP.NET**. Es entspricht auf LAMP-Systemen der Skriptsprache PHP. Als Datenbank wird der Microsoft SQL-Server genutzt.   
WIMA steht für: **Windows**, **Internet Information Server**, **Microsoft SQL Server**, **ASP.NET**)  

Wichtige Komponenten des Internet Information Services sind:

* Protokoll-Listener wie HTTP.sys
* World Wide Web Publishing Services (WWW-Service)
* Windows Process Activation Services (WAS)