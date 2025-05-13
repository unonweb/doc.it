---
title: SMB
tags: "smb"
---

SMB
===========

* Client-Server-Protokoll
* Arbeitet mit eigenem Netzwerkdateisystem; ist damit unabhängig vom Dateisystem des Servers
* Der SMB-Client kann Kommandos an den SMB-Server übertragen, ähnlich wie das FTP unterstützt er den interaktiven Datenaustausch, die Druckerfreigabe und das Drucken, und kann zur Fehlersuche und zur Fehlerbeseitigung beitragen. 

SMB implementiert ein Netzwerkdateisystem ähnlich wie NFS und ist damit vom zugrundeliegenden Dateisystem des Servers größtenteils unabhängig. Höhere Protokolle wie DFS setzen wiederum auf SMB auf. 

In TCP/IP-Netzen wurde SMB ursprünglich in NetBIOS over TCP/IP (NBT) über die TCP/UDP-Ports 137–139 gekapselt, die Namensauflösung erfolgte häufig mittels WINS bzw. Broadcasts. Heutige Windows-Versionen nutzen SMB direkt auf dem TCP-Port 445 und lösen Namen per DNS und in kleinen Netzwerken per LLMNR auf. 

#### Nachrichtentypen

Sieht vier Nachrichtentypen vor:
* Sitzungssteuerung
* Datei
* Drucker
* Nachricht für die Kommunikation zwischen Client und Server

Transportprotokoll: entweder *TCP* oder *NetBIOS over TCP*

#### Versionen

* **SMB 2.0**: Windows Vista und Windows Server 2008
* **SMB 2.1**: Windows 7 und das zweite Release von Windows Server 2008
* **SMB 3.0**: Windows 8 und Windows Server 2012
  * Verschlüsselung

#### Versteckte Windows-Freigaben
Das Programm "fsmgmt.msc" zeigt neben den selbst angelegten auch versteckte Freigaben an. Sie erkennen versteckte Freigaben an dem Dollarzeichen hinter dem Freigabenamen. Solche Freigaben zeigt Windows unter "Netzwerk" nicht an. 
* versteckte Freigaben: *`ADMIN$`*, *`IPC$`* und *`C$`*
* auch administrative Freigaben genannt. 
* Laut Microsoft sollten diese nicht aufgehoben werden, da es sonst zu Fehlern in Windows kommen kann. 