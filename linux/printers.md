---
title: Printers
---

Common
----------

`/etc/printcap/` *printer capability database*; hier hat jeder Drucker, der an das System angeschlossen ist, aber auch alle Drucker, die über das Netz erreichbar sein sollen, einen eigenen Eintrag.

Druckaufträge werden von CUPS oder LDP unter `/var/spool/…` (Druckerwarteschlange) abgeholt. Hier liegen die Daten im .raw-Format. Sie werden dann einem Druckfilter übergeben, der, in Abhängigkeit vom verwendeteten Drucker, einen passenden Datenstrom generiert.

CUPS
---------

* *Common Unix Printing System*; kompatibel zu LPR
* Client/Server-System
* Webfrontend: http://localhost:631
* *cupsd* Daemon/Scheduler
* Ein Rechner mit CUPS kann im Netzwerk als Drucker-Server für Windows-Maschinen dienen.


Config files:
* Konfigurationsdatei daemon: `/etc/cups/cupsd.conf`
* Konfigurationsdatei printers: `/etc/cups/printers.conf`