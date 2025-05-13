---
title: Systemvariablen
---

SYSTEMVARIABLEN
==========================


`sysdm.cpl` >> Erweitert >> *Umgebungsvariablen* 

### File system
* `appdata`      #   
    z.B. *C:\Users\uno\AppData\Roaming*   
* `systemroot`   # path to the current Windows installation
    z.B. *C:\Windows*  
* `windir`  #  path to the current Windows installation
    z.B. *C:\Windows*  
* `userprofile`  #   
    z.B. *C:\Users\uno*  
* `psmodulepath` # reserved for modules that ship with Windows  
    z.B. *C:\Windows\System32\WindowsPowerShell\v1.0\Modules*  
* `temp` #   
    z.B. *C:\Users\uno\AppData\Local\Temp*  
    Finden sich hier durchnummerierte Ordner, weist dies auf RDP-Sitzungen hin


* `ClientName` # name of the remote computer connected via a Remote Desktop session.
* `SessionName`# helps to identify if the current Windows session is regarded by the operating system as running at the console. For console sessions SessionName will be *Console*. Enhanced Session connections to Hyper-V Virtual Machines do not report SessionName as ‘Console’, whereas Standard Sessions do.

* `UserDNSDomain` # The Fully Qualified Domain Name of the Active Directory domain that the current user logged on to. Only present for domain logons.


Process Scope
------------------

Environment variables in the process scope are a combination of the machine and user scopes, along with some variables that Windows creates dynamically.

Below is a list of environment variables available to a running process. All of these variables are dynamically created.

* ALLUSERSPROFILE
* APPDATA
* COMPUTERNAME
* HOMEDRIVE
* HOMEPATH
* LOCALAPPDATA
* LOGONSERVER
* PROMPT
* PUBLIC
* SESSION
* SystemDrive
* SystemRoot
* USERDNSDOMAIN
* USERDOMAIN
* USERDOMAIN_ROAMINGPROFILE
* USERNAME
* USERPROFILE