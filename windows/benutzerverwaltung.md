---
title: Benutzerverwaltung
---

Benutzerverwaltung
==================

### Anmeldung

* Lokale Anmeldung >> **Security Account Manager** (SAM)  
* Arbeitsgruppe >> Zusammenschluss mehrerer gleichberechtigter Computer. Daten können nur für Benutzer freigegeben werden, die im lokalen SAM hinterlegt sind. In jedem lokalen SAM müssen alle Konten aller Benutzer geführt werden. Eine Arbeitsgruppe kommt daher nur für kleine Netzwerke in Frage (~ 5 Computer).
* Anmeldung an einem **Domain Controler** >> zentrale Datenbank (**Active Directory**) für Anmeldeinformationen

#### SAM
is a database file that stores users' passwords  
`%SystemRoot%/system32/config/SAM` # mounted on HKLM/SAM


Benutzerprofile / User profiles
---------------------------------


#### Inhalt
* Desktopeinstellungen
* Installierte Programme
* Hintergrundbild

#### ntuser.dat
`%userprofile%\NTUSER.DAT` #
`ntuser.dat` # Enthält alle wichtigen benutzerdefinierten Einstellungen. Diese sind meistens auch mit der Registry verbunden.

### Speicherort

`%USERPROFILE%` # user profile path  

#### lokal
`C:\Users\<user>` # local profile path  

#### remote

* Profil-Server: Freigabe erstellen
* AD: Pfadangabe im Benutzerkonto anpassen

Das Profil wird bei der Anmeldung auf den lokalen Rechner kopiert. D.h., dass der Benutzer, während er angemeldet ist, mit der lokalen Kopie seines Profils arbeitet. Bei der Abmeldung wird das Profil wieder zum Profil-Server übertragen.

#### remote [mandatory]

Um dem Benutzer eine Änderung seines Profils zu verbieten, muss (nur) dafür gesorgt werden, dass die Datei `%userprofile%\NTUSER.DAT` nicht verändert werden kann. Dies kann durch einfaches Umbenennen der Datei erreicht werden: 
`NTUSER.DAT` >> `NTUSER.MAN` (= *mandatory*)  
Die lokale Kopie kann (nach der Anmeldung) weiterhin verändert werden, sie wird jedoch bei der Abmeldung nicht mehr auf dem Server gespeichert, da die Datei `NTUSER.MAN` schreibgeschützt ist. 


ACCESS CONTROL
===================


User Account Control / Benutzerkontensteuerung
----------------------------------------------------

* sorgt dafür, dass jeder Prozess selbst dann nur mit eingeschränkten Rechten läuft, wenn Ihr Konto Administratorrechte besitzt.
* Sobald eine Anwendung administrative Berechtigungen für ihre Ausführung anfordert, wird ein Dialogfeld angezeigt, welches explizit zu bestätigen ist, um die Rechte zu gewähren.  

Die Benutzerkontensteuerung schützt nicht per se vor Malware, sie sorgt nur für eine strikte Trennung zwischen Benutzer- und Administratorrechten.
Jedes Mal, wenn die Grenze von unten nach oben durchschritten werden soll, erfolgt eine UAC-Abfrage. Der „Schutz“ besteht also darin, dass man bestimmen kann, welches Programm Adminrechte bekommt und welches nicht.

#### least-privileged user account (LUA)
Mit Windows Vista, welches erstmals *Microsofts Security Development Lifecycle* anwendete, wurde mit der Benutzerkontensteuerung ein vergleichbarer Mechanismus eingeführt, um das Prinzip des least user access oder least-privileged user account (LUA) umzusetzen. 

Mitglieder der Gruppe Administratoren erhalten beim Anmelden zwei Token: Einen als Administrator, und einen als Standardnutzer, dem alle administrativen Rechte und Privilegien entzogen sind. Diese Nutzer werden als „Geschützte Administratoren“ (*Protected Administrators*, PA) bezeichnet. 

Während die Sudoers unter Unix den „großen Bruder“ mit sudo anrufen, haben die Geschützten Administratoren eine gespaltene Persönlichkeit”, welche sie je nach Aufgabe wechseln. Um eine Manipulation der Rechteerhöhung durch Schadsoftware zu vermeiden, findet diese standardmäßig auf dem *sicheren Desktop* statt.  

#### runas
In die Benutzerkontensteuerung wurde auch der Befehl **Runas** implementiert. Der Befehl kann dazu genutzt werden, Verwaltungs- bzw. Administrationsaufgaben durchzuführen, ohne dass sich ein Benutzer mit Administrationsrechten komplett neu anmeldet. Dazu muss das Konto und das Passwort eines Mitgliedes der Gruppe Administratoren eingegeben werden.  
Diese UAC-Abfrage wird als „*Eingabeaufforderung für erhöhte Rechte für Standardbenutzer“* (Over-the-shoulder, OTS) bezeichnet, während die Rechteerhöhung von Geschützten Administratoren als „Administratorbestätigungsmodus“ (Admin Approval Mode, AAM) bezeichnet wird.   

#### Integriertes Admin-Konto
Das Konto „Administrator“ unterscheidet sich von anderen Admins: 
* lässt es sich nicht löschen
* greift die Benutzerkontensteuerung bei diesem Konto nicht. 

Wenn unter dem Konto „Administrator“ ein Programm gestartet wird, verfügt es also von vornherein über Admin-Rechte. Das gilt auch für den automatisch als Shell gestarteten Explorer. Für jemanden, der die Benutzerkontensteuerung nicht kennt, mag das im Ergebnis so wirken, als wäre der „Administrator“ mächtiger als andere Admins, in Wirklichkeit fehlt aber nur der Schutz durch die UAC.


### Sichere Anmeldung mit Strg+Alt+Entf

Sie können für erhöhte Sicherheit anfordern, dass Benutzer vor der Anmeldung die Tastenkombination STRG+ALT+ENTF drücken. Dies gewährleistet das Anzeigen der authentischen Windows-Anmeldeseite und schützt das System vor Programmen, die durch das Darstellen einer Anmeldung versuchen, Kennwortinformationen abzurufen.


Windows security model
------------------------

### Security principals

Security principals are any entity that can be authenticated by the operating system, such as 
* a user account
* a computer account
* a thread or process that runs in the security context of a user or computer account
* the security groups for these accounts. 

Each security principal is represented in the operating system by a unique *Security identifier* (**SID**).

### Security Identifier (SID)
> A SID is used to uniquely identify a *security principal* that represents any entity that can be authenticated by the system. 

* is issued by an authority, such as a Windows domain controller. It is stored in a security database.
* The SID for a *local account* or group is generated by the Local Security Authority (LSA) on the computer, and it is stored with other account information in a secure area of the registry.

#### Aufbau
S-1-5-32-544 # built-in Administrators group
1. Indicates that the string is a SID
2. *revision level*
3. *identifier authority value*
   * 0 = SECURITY_NULL_SID_AUTHORITY
   * 1 = SECURITY_WORLD_SID_AUTHORITY
   * 2 = SECURITY_LOCAL_SID_AUTHORITY
   * 3 = SECURITY_CREATOR_SID_AUTHORITY
   * 5 = NT Authority
4. *domain identifier*
   * 32 = Builtin
5. *relative identifier*
   * 544 = Administrators

### well-known SIDs

SID                 |                               | Description
--------------------|-------------------------------|----------------
**S-1-0**           |**Null Authority**|An identifier authority.
S-1-0-0             |*Nobody*|A group with no members. This is often used when a SID value is not known.
**S-1-1**           |**World Authority**
S-1-1-0 	        |*Everyone*|A group that includes all users, even anonymous users, and guests. Membership is controlled by the operating system.
**S-1-2**           |**Local Authority**|An identifier authority.
S-1-2-0 	        |*Local*|A group that includes all users who have logged on locally.
S-1-2-1 	        |Console Logon|A group that includes users who are logged on to the physical console.
**S-1-3**           |**Creator Authority**|
S-1-3-0 	        |Creator Owner|A placeholder in an inheritable access control entry (ACE). When the ACE is inherited, the system replaces this SID with the SID for the object's creator.
S-1-3-4             |Owner Rights|A group that represents the current owner of the object. When an ACE that carries this SID is applied to an object, the system ignores the implicit READ_CONTROL and WRITE_DAC permissions for the object owner.
**S-1-4**           |**Non-unique Authority**|An identifier authority.
**S-1-5**           |**NT Authority**|A SID that represents an identifier authority.
S-1-5-2             |*Network*|A group that includes all users that have logged on through a network connection. Membership is controlled by the operating system.
S-1-5-3             |Batch|A group that includes all users that have logged on through a batch queue facility. Membership is controlled by the operating system.
S-1-5-4             |Interactive|A group that includes all users that have logged on interactively. Membership is controlled by the operating system.
S-1-5-5-X-Y         |Logon Session|A logon session. The X and Y values for these SIDs are different for each session.
S-1-5-6             |*Service*|A group that includes all security principals that have logged on as a service. Membership is controlled by the operating system.
S-1-5-7             |Anonymous|A group that includes all users that have logged on anonymously. Membership is controlled by the operating system.
S-1-5-9             |Enterprise Domain Controllers|A group that includes all domain controllers in a forest that uses an Active Directory directory service. Membership is controlled by the operating system.
S-1-5-10            |Principal Self|A placeholder in an inheritable ACE on an account object or group object in Active Directory. When the ACE is inherited, the system replaces this SID with the SID for the security principal who holds the account.
S-1-5-11            |Authenticated Users|A group that includes all users whose identities were authenticated when they logged on. Membership is controlled by the operating system.
S-1-5-18            |*Local System*|A service account that is used by the operating system.
S-1-5-19 	        |NT Authority|Local Service
S-1-5-20            |NT Authority|Network Service
**S-1-5-21**        |**NT-Domain**
S-1-5-21domain-500  |*Administrator*|A user account for the system administrator. By default, it is the only user account that is given full control over the system.
S-1-5-21domain-501  |*Guest*|A user account for people who do not have individual accounts. This user account does not require a password. By default, the Guest account is disabled.
S-1-5-21domain-512  |*Domain Admins*|A global group whose members are authorized to administer the domain. By default, the Domain Admins group is a member of the Administrators group on all computers that have joined a domain, including the domain controllers. Domain Admins is the default owner of any object that is created by any member of the group.
S-1-5-21domain-513  |*Domain Users*|A global group that, by default, includes all user accounts in a domain. When you create a user account in a domain, it is added to this group by default.
S-1-5-21domain-514  |*Domain Guests*|A global group that, by default, has only one member, the domain's built-in Guest account.
S-1-5-21domain-515  |*Domain Computers*|A global group that includes all clients and servers that have joined the domain.
**S-1-5-32**        |**NT-Builtin**
S-1-5-32-544        |*Administrators*|A built-in group. After the initial installation of the operating system, the only member of the group is the Administrator account. When a computer joins a domain, the Domain Admins group is added to the Administrators group. When a server becomes a domain controller, the Enterprise Admins group also is added to the Administrators group.
S-1-5-32-545        |*Users*/*Benutzer*|A built-in group. After the initial installation of the operating system, the only member is the Authenticated Users group. When a computer joins a domain, the Domain Users group is added to the Users group on the computer.
S-1-5-32-547        |*Power Users*|A built-in group. By default, the group has no members. Power users can create local users and groups; modify and delete accounts that they have created; and remove users from the Power Users, Users, and Guests groups. Power users also can install programs; create, manage, and delete local printers; and create and delete file shares.
S-1-5-32-548 	    |*Account Operators*|A built-in group that exists only on domain controllers. By default, the group has no members. By default, Account Operators have permission to create, modify, and delete accounts for users, groups, and computers in all containers and organizational units of Active Directory except the Builtin container and the Domain Controllers OU. Account Operators do not have permission to modify the Administrators and Domain Admins groups, nor do they have permission to modify the accounts for members of those groups.
S-1-5-32-549        |*Server Operators*|A built-in group that exists only on domain controllers. By default, the group has no members. Server Operators can log on to a server interactively; create and delete network shares; start and stop services; back up and restore files; format the hard disk of the computer; and shut down the computer.
**S-1-5-80**        |**NT Service**|An NT Service account prefix.
S-1-5-80-0 	        |*All Services*|A group that includes all service processes configured on the system. Membership is controlled by the operating system.



Each time a user signs in, the system creates an **access token** for that user. The access token contains the user’s SID, user rights, and the SIDs for groups that the user belongs to. This token provides the security context for whatever actions the user performs on that computer.


Service User Accounts
------------------------

* predefined local accounts used by the service control manager

Each service executes in the security context of a user account. The service control manager (SCM) automatically loads the user profile. When starting a service, the SCM logs on to the account associated with the service. If the log on is successful, the system produces an access token and attaches it to the new service process. This token identifies the service process in all subsequent interactions with securable objects.

You can specify one of the following special accounts instead of specifying a user account for the service:

* LocalService
* NetworkService
* LocalSystem


### NetworkService Account

* has minimum privileges on the local computer and acts as the computer on the network.

A service that runs in the context of the NetworkService account presents the computer's credentials to remote servers.  
By default, the remote token contains SIDs for the *Everyone* and *Authenticated Users* groups.


### LocalService Account

[docs.microsoft.com]([https://link](https://docs.microsoft.com/de-de/windows/win32/services/localservice-account))
* has minimum privileges on the local computer and presents anonymous credentials on the network.


### LocalSystem account

* has extensive privileges on the local computer, and acts as the computer on the network.
 
Its token includes the *NT AUTHORITY\SYSTEM* and *BUILTIN\Administrators* SIDs; these accounts have access to most system objects. 

The name of the account in all locales is *.\LocalSystem*.
Alias: *LocalSystem*, *ComputerName\LocalSystem*
This account does not have a password.