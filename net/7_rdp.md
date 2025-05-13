# RDP

**Remote Desktop Protocol**

* ein von Microsoft entwickeltes Protokoll für den Remote-Zugriff auf einen Rechner mit Windows-Betriebssystem
* verschlüsselte Übertragung von Bildschirmausgaben und Steuerungsbefehlen über IP-Netze
* arbeitet nach dem Client-Server-Prinzip: das eine System fungiert als Terminalserver, das andere als Client

Damit sich eine RDP-Sitzung aufbauen lässt, müssen sowohl die Firewall des Netzwerks als auch die Firewall auf dem Server Verbindungen von außen zulassen. Kommt NAT auf der Strecke zwischen Client & Server zum Einsatz, benötigt der Remote-Rechner die öffentliche IP-Adresse, unter der der Server erreichbar ist. Zudem muss eine Portweiterleitung auf dem NAT-Router in Richtung des Servers eingerichtet sein.

# TROUBLES

* user may only be logged in once!


# WINDOWS

* **mstsc.exe**
* RDP nutzt den TCP-Port 3389
* `Systemsteuerung\System und Sicherheit\System` --> Remoteeinstellungen
* **Remoteunterstützung**: Einen Außenstehenden in sein System einzuladen (für Remote-Wartungssitzungen)
* **Remotedesktopverbindung**: Einloggen in das gewünschte Host-System ohne aktives „Zuschalten“

## Voraussetzungen

* Firewall-Ausnahme
* Router: Port-Freigabe & - Weiterleitung

# CLIENTS

* remmina
* rdesktop
* vinagre

## rdesktop 

ist ein Open-Source-Client für die *Remotedesktop-Dienste von Windows* (früher auch *Terminal Services*). Durch Nutzung dieses Programms ist es möglich, im lokalen Netzwerk oder von unterwegs via UMTS und einer VPN-Verbindung auf den Desktop eines Windows-Rechners (z.B. in der Firma) zuzugreifen.  
Im Gegensatz zu VNC überträgt das Remote Desktop Protokoll (RDP) nicht den Bildschirminhalt des Servers, sondern startet für jeden Benutzer einen neuen, nicht am Bildschirm des Servers sichtbaren, virtuellen Bildschirm.

So wird eine Möglichkeit eröffnet, Windowsprogramme unter Linux zu nutzen und nicht z.B. über Wine installieren zu müssen.


