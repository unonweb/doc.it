---
title: "Domain Controller"
---

Active Directory
=====================


Aufbau
-----------------------

### Hierarchie ###

* **Domäne/Domain**: Basiseinheit der Verwaltung; Zusammenschluss mehrerer Computer zu einer administrativen Einheit
* **Struktur/Tree**: hierarchische Struktur von Domänen (Domain + Subdomains) verbunden über Vertrauensstellungen, die den domänenübergreifenden Zugriff auf Ressourcen erlauben
* **Gesamtsstruktur/Forest**: 

#### forest ####
![ad forest](H:/doc/img/ad_forest.PNG)


### Objekte & Attribute ###

Die Datensätze in der Datenbank werden in Active Directory als *Objekte* und deren Eigenschaften als *Attribute* definiert.  
Die Attribute sind abhängig von ihrem Typ definiert. Objekte werden eindeutig über ihren Namen identifiziert. 

AD verwaltet verschiedene Objekte in einem Netzwerk wie z.B.  
Benutzer, Gruppen, Computer, Dienste, Server, Dateifreigaben und Geräte wie Drucker und Scanner und deren Eigenschaften.


### Schema, Konfiguration & Domain ###

* **Schema**: Eine Schablone für alle Active-Directory-Einträge. Es definiert sowohl Objekttypen, ihre Klassen und Attribute als auch ihre Attributsyntax. Welche Objekttypen in Active Directory verfügbar sind, lässt sich durch die Definition neuer Typen beeinflussen. Das dafür zugrundeliegende Muster ist das „Schema“, das die Objekte und ihre Attribute definiert.
* **Konfiguration**: beschreibt die Active-Directory-Gesamtstruktur und deren Bäume.
* **Domain**: enthält alle Informationen, die sie selbst und die in ihr erstellten Objekte beschreiben.


### Pfade ###

* **Datenbank**: `C:\Windows\NTDS\` # Datenbankdatei: NTDS.DIT
* **Protokolldateien**: Idealerweise auf einer anderen Festplatte
* **SYSVOL**: `C:\Windows\SYSVOL` # Freigabe auf jedem DC. Informationen für die Clients. Jeder Client greift beim Anmeldevorgang darauf zu und liest hier Informationen wie z.B. die Gruppenrichtlinien aus


AD Serverrollen
-----------------------

Seit Windows Server 2008 sind unter dem Begriff Active Directory fünf verschiedene Serverrollen zusammengefasst:
* *Active Directory Domain Services* (**AD DS**): die aktuelle Version des ursprünglichen Verzeichnisdienstes und der zentrale Punkt der Domain- und Ressourcenverwaltung.  
* *Active Directory Lightweight Directory Services* (**ADLDS**): eine funktional eingeschränkte Version des AD DS, die der Anbindung von Anwendungen oder Diensten dient, die LDAP-konforme Informationen aus dem Verzeichnis benötigen.
* *Active Directory Federation Services* (Verbunddienste, **ADFS**): dienen der webgestützten Authentifizierung von Benutzern, wenn diese sich in Bereichen außerhalb der AD-DS-Infrastruktur befinden.
* *Active Directory Rights Management Services* (**ADRMS**) schützen Ressourcen durch kryptografische Methoden gegen unbefugte Einsicht.
* *Active Directory Certificate Services* (**ADCS**) stellen eine Public-Key-Infrastruktur bereit.


Active Directory & DNS
-----------------------

* Die Installation von AD setzt einen DNS-Server zwingend voraus  
![active directory and dns relation](img/dc_ad_and_dns_relation.png)


#### SRV-Einträge
Identifizierung von Diensten per DNS-Eintrag. Bei seiner Installation trägt der Anmeldedienst des DC seine Dienste im DNS-Server ein:
* **Kerberos**: Dienst für die Anmeldung  
* **LDAP**: Dienst für die Abfrage des Verzeichnisdienstes   
**Clients können sich an der Domäne nicht anmelden, wenn keine passenden SRV-Einträge vorhanden sind.**


Technische Aspekte
-----------------------

### Ports

* **389**: LDAP
* **686**: LDAP-SSL


Sicherheit
--------------

### LDAP-Signing

* LDAP-Signaturen stellen sicher, dass Daten auf dem Weg vom Absender zum Empfänger nicht verändert werden.  
  Der Absender signiert Daten kryptographisch, der Empfänger kann die Signatur verifizieren.
* Schutz gegen Replay & Man-in-the-Middle: Daten durch Abhören der Netzwerkkommunikation abfangen und in veränderter Form an das eigentliche Ziel weiterleiten


1. Die Ereignisanzeige der Domain Controller auf Eventeinträge mit der ID 2887 überprüfen. Dieser Eventeintrag wird erstellt, wenn Systeme noch per LDAP ohne Signing Anfragen senden. Der Eintrag wird einmal täglich im Eventlog erstellt.
2. HowTo: Umstellung auf LDAP-Signing:  
https://www.abtis.de/umstellung-der-domain-controller-auf-ldap-channel-binding-und-ldap-signing/


### LDAP-Channel-Binding

Benutzer- und Computerkonten
=====================

`dsa.msc`

### Benutzerkonten

Einige Anwendungen benutzen das AD als Datenbank (Exchange Server). Daher hat ein Benutzerkonto viele Attribute.

#### Benutzerprofile

* Benutzerprofile zentral auf dem Server verwalten:
  1) Auf dem "Profil-Server" eine Freigabe erstellen
  2) Im AD Benutzerkonto den Profilpfad anpassen
  3) Optional: Die Datei `NTUSER.DAT` in `NTUSER.MAN` umbennen (um das Benutzerprofil schreibgeschützt zu machen)

#### Home-Directory
(Basisordner): Default: `%Userprofile%\Eigene Dateien`

* Alle vom Benutzer erzeugten Dokumente sollen auf dem Server und nicht auf der lokalen Festplatte gespeichert werden:
  - Ein Basisverzeichnis auf einem Server zuweisen
  - Gruppenrichtlinie *Ordnerumleitung* (verknüpft den Ordner *Eigene Dateien* mit dem Basisordner auf dem Server)  

#### Benutzerkonto kopieren

Achtung: Es werden nicht alle Einstellungen übernommen!  

Übernommen wird...
* *Allgemein* << kein Eintrag!
* *Adresse* << Alle Einträge bis auf *Straße*
* *Konto* << Alle Einträge bis auf *Benutzeranmeldename*
* *Profile* << Alle Einträge (Variable werden angepasst)
* *Rufnummern* << kein Eintrag
* *Organisation* << Alle Einträge bis auf *Position*
* *Mitglied von* << Alle Einträge

Alle anderen Einträge auf allen anderen Seiten werden nicht übernommen.

#### Benutzerkontovorlagen
Kontovorlagen erstellen, die nur kopiert und um die individuellen Angaben ergänzt werden müssen.  
* Kontovorlagen mit einem vorangestellten _ benennen
* Ein Konto das nur als Vorlage dienen soll deaktivieren

### Computerkonten

#### Computer hinzufügen

a) DC: Für jeden Computer muss ein Konto im AD hinterlegt sein  
b) Lokal: Jeder Computer benötigt einen Eintrag in der Registry, dass er ein Domänenmitglied ist

2 Möglichkeiten:  
a) manuell: Zuerst das Computerkonto im AD erstellen und den Computer dann lokal zum Domänenmitglied machen  
b) automatisch: Das Computerkonto im AD beim Hinzufügen zur Domäne automatische erstellen lassen 


#### Startsequenz

* Der Computer liest in seiner lokalen Registry, dass er Mitglied einer Domäne ist
* Der Computer nimmt Kontakt zu einem Domänencontroller auf 
* Der DC überprüft, ob der Computer ein gültiges Konto im AD hat
* Falls ja, wird das Anmeldefenster angezeigt

### Anmeldung

Der Account *administrator* ist i.d.R. sowohl lokal als auch in der Domäne vorhanden.  
`<domain>\<user>`   # Anmeldung an der Domäne  
`.\<user>`          # Anmeldung lokal
`<computer>\<user>` # Anmeldung lokal oder remote

#### Cached domain logon information

Windows caches previous users' logon information locally so that they can log on if a logon server is unavailable during later logon attempts.

Cached logon information is controlled by the following key:

* Location: HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\Current Version\Winlogon\
* Value name: CachedLogonsCount
* Data type: REG_SZ
* Values: 0 - 50


### Gruppen

* Globale Gruppen
* Domänenlokale Gruppen
* Verteilergruppen (E-Mail)
* Sicherheitsgruppen

### Lokale Adminrechte

#### Möglichkeit 1

1) Domänen-Gruppe *Local-Admins* erstellen
2) Diese Gruppe auf jedem Client der lokalen Gruppe *Administratoren* hinzufügen




Group Policy / Gruppenrichtlinie
==================================


Mit Gruppenrichtlinien können Administratoren wichtige Computer- und Benutzereinstellungen zentral steuern und diese über das Netzwerk verteilen. 

### Anwendungreihenfolge von GPs

* Lokale Richtlinie / Local policies
* Standort / Site policies
* Domäne
* OU

### SYSVOL

`\\<server>\sysvol\<domain>` # enthält die Gruppenrichtlinien und Skripte für \<domain\>

### .admx / .adml
* *.admx*: administrative templates
* *.adml*: language files
* create and administer registry-based policy settings

#### Group Policy Central Store
* a repository of *.adml* and *.admx* files that are stored in the *SYSVOL* folder of your domain.
* a file location that is checked by the Group Policy tools by default
* one location to distribute *.admx* files 
* instead of copying the new ADMX files to the *C:\Windows\PolicyDefinitions* folder on each of your DC’s and the ADML files to the correct language sub-folder.

`\\<server>\sysvol\<domain>\\policies\PolicyDefinitions`

### RSoP
`rsop.msc` # *Resultant Set of Policy*; gather only computer-specific policy information.


Powershell
--------------

`get-gpo`  


### Update / Aktualisierung
Durchgeführte Änderungen an den Gruppenrichtlinien aktualisieren sich auf Computern im Active Directory automatisch. Entweder nach 90 Minuten oder einem Neustart des Rechners. Glücklicherweise lässt sich das Ganze aber auch beschleunigen.

#### gpupdate

`gpupdate /Force` # alle Richtlinien nochmal anwenden, die sich seit der letzten Ausführung geändert haben.  
`gpupdate /Target:{Computer | User}` # entweder nur Computerrichtlinien oder nur Benutzerrichtlinien erneut angewenden. Standardmäßig werden beide aktualisiert.

#### gpedit
`gpedit` #

Struktur
--------------

* **Softwareeinstellungen**:- Über diesen Eintrag lassen sich Applikationen automatisch verteilen, deren Installation auf .msi-Dateien beruhen.
* **Windows-Einstellungen**: In diesem Bereich befinden sich die meisten Einstellungen, die vorgenommen werden können. Für jede Einstellung finden sich zahlreiche Erklärungen.
* **Administrative Vorlagen**: Hier finden sich Möglichkeiten zur Einstellung und Automatisierung von Windows-Server und Windows-Arbeitsstationen. Hier lassen sich Einstellungen im Explorer, dem Desktop und vielen anderen Funktionen in Windows vornehmen, natürlich auch Sicherheitseinstellungen.

#### Sonstiges
* **Starter-Gruppenrichtlinienobjekte**. Bei diesen Richtlinien handelt es sich um schreibgeschützte Vorlagen, die bei der Erstellung von neuen Richtlinien genutzt werden können.


Empfohlene Einstellungen
----------------------------


### Computerkonfiguration


**\>> Windows-Einstellungen \>> Sicherheitseinstellungen \>> Kontorichtlinien \>> Kennwortrichtlinien**

* *Kennwort muss Komplexitätsvoraussetzungen entsprechen*
* *Kennwortchronik erzwingen*  
   sicherstellen, dass alte Kennwörter nicht ständig wieder verwendet werden.
* *Minimale Kennwortlänge*

**\>> Windows-Einstellungen \>> Sicherheitseinstellungen \>> Kontorichtlinien \>> Kontosperrungsrichtlinien**

* *Kontensperrungsschwelle*

**\>> Windows-Einstellungen \>> Sicherheitseinstellungen \>> Lokale Richtlinien \>> Sicherheitsoptionen**

* *Benutzerkontensteuerung: Administratorgenehmigungsmodus für das integrierte Administratorkonto*
* *Benutzerkontensteuerung: Alle Administratoren im Administratorgenehmigungsmodus ausführen*
* *Benutzerkontensteuerung: Anwendungsinstallationen erkennen und erhöhte Rechte anfordern*
* *Benutzerkontensteuerung: Bei Benutzeraufforderung nach erhöhten Rechten zum sicheren Desktop wechseln* // *User Account Control: Switch to the secure desktop when prompting for elevation*

**\>> Windows-Einstellungen \>> Sicherheitseinstellungen \>> Lokale Richtlinien \>> Überwachungsrichtlinie**

* *Objektzugriffsversuche überwachen*  
  Legt fest, ob Benutzerzugriffe auf Nicht-Active Directory-Objekte vom Betriebssystem überwacht werden. Eine Überwachung wird nur für Objekte generiert, für die eine SACL (System Access Control List, System-Zugriffssteuerungsliste) angegeben ist, und nur dann, wenn der angeforderte Zugriffstyp (beispielsweise Schreiben, Lesen oder Ändern) und das Konto, von dem die Anforderung stammt, den Einstellungen in der SACL entsprechen.

**\>> Windows-Einstellungen \>> Sicherheitseinstellungen \>> Lokale Richtlinien \>> Windows Defender Firewall mit erweiterter Sicherheit**


**\>> Windows-Einstellungen \>> Sicherheitseinstellungen \>> Anwendungssteuerungsrichtlinien \>> AppLocker**  
Zugriffssteuerung für Anwendungen



**\>> Administrative Vorlagen \>> System \>> Benuterprofile**

* *Benutzerprofile, die älter als eine bestimmte Anzahl von Tagen sind, beim Systemneustart löschen*

**\>> Administrative Vorlagen \>> System \>> Wechselmedienzugriff**

* *Wechseldatenträger: Lesezugriff verweigern*
* *Wechseldatenträger: Schreibzugriff verweigern*
* *Wechseldatenträger: Ausführungszugriff verweigern*  
  Verhindert zwar die Ausführung von .exe-Dateien über den USB-Stick, der Benutzer könnte diese aber durch den vorhandenen Lesezugriff vor der Ausführung auf die lokale Festplatte kopieren. Zudem verhindert diese Richtlinie nicht das Öffnen anderer Dateien.

**\>> Administrative Vorlagen \>> Systemsteuerung \>> Anpassung**

* *Sperrbildschirm nicht anzeigen*


### Benutzerkonfiguration

**\>> Administrative Vorlagen \>> System**

* *Angegebene Windows-Anwendungen nicht ausführen*
* *Zugriff auf Programme zum Bearbeiten der Registrierung verhindern*  
  Wer Registry-Schlüssel über ein Loginskript importiert, der kann die Option zur Ausführung im Hintergrund aktivieren. Dann funktionieren diese Importe beim Login auch weiterhin.
* *Zugriff auf Eingabeaufforderung verhindern*

**\>> Administrative Vorlagen \>> Systemsteuerung**

* *Angebenene Systemsteuerungssymbole ausblenden*
* *Nur angebebene Systemsteuerungssymbole anzeigen*

**\>> Administrative Vorlagen \>> Systemsteuerung \>> Anpassung**

* Bildschirm automatisch sperren:
  * *Bildschirmschoner aktivieren* 
  * *Zeitlimit für Bildschirmschoner*  
    Gibt an, wie viel Zeit im Leerlauf vergehen muss, bis der Bildschirmschoner gestartet wird.  
  *  *Kennwortschutz für den Bildschirmschoner verwenden*

**\>> Administrative Vorlagen \>> Windows-Komponenten \>> Windows-Update**

* *Zugriff auf alle Windows Update-Funktionen entfernen*


Netzwerk
-----------

#### Computerkonfiguration \>> Administrative Vorlagen \>> Netzwerk \>> Verbindungsschicht-Topologieerkennung
(Computer \>> Administrative Templates \>> Network \>> Link-Layer Topology Discovery)  

* *Treiber für die Zuordnungs-E/A (LLTDIO) aktivieren* #  
* *Turn on Mapper I/O (LLTDIO) driver*) # allows the computer to find network resources such as file shares  
*Treiber für den Beantworter (RSPNDR) aktivieren* # (*Turn on Responder (RSPNDR) driver*) # allows other computers to find this one  



How To
---------------

#### Netzlaufwerk verbinden
Name der GPO: *[User] Netzlaufwerk verbinden* # Daraus geht hervor, dass es sich um eine Benutzerrichtlinie handelt und, dass ich damit ein Netzlaufwerk verbinden möchte.

#### Dateisystemzugriffe überwachen

1) Überwachung per Gruppenrichtlinie generell aktivieren:  
   \>> Windows-Einstellungen \>> Sicherheitseinstellungen \>> Lokale Richtlinien \>> Überwachungsrichtlinie \>> Objektzugriffsversuche überwachen
2) Die Überwachung eines Objekts konfigurieren:
   \>> Rechtsklick auf das zu überwachende Objekt \>> Eigenschaften \>> Sicherheit \>> Erweitert \>> Überwachung
3) Die Überwachung wird in der Ereignisanzeige protokolliert (`eventvwr.msc`)


Administration
---------------------

### GPMC (Group Policy Management Console)

Gruppenrichtlinienverwaltung

Snap-In, das eine erweiterte und verbesserte Konfiguration von Gruppenrichtlinien erlaubt, allerdings zuerst lokal auf dem Computer installiert werden muss (auf Domänencontrollern mit Benutzeroberfläche wird sie stets mitinstalliert).

### AGPM (Advanced Group Policy Management)

part of the *Microsoft Desktop Optimization Pack* (MDOP), which is an add-on to an Enterprise Agreement with SA