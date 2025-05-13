---
title: LLMNR
---

LLMNR
---------

**Link Local Multicast Name Resolution** ist ein Protokoll basierend auf dem DNS-Paketformat, das IPv4 und IPv6 Hosts die Namensauflösung für Hosts im selben lokalen Netzwerk erlaubt.

* Unter Linux ist es im *systemd-resolved* implementiert.

Um auf Anfragen zu antworten, lauschen Empfänger auf **UDP-Port 5355** in den folgenden Multicast-Adress-Bereichen:

* IPv4 – `224.0.0.252`, MAC-Adresse – `01-00-5E-00-00-FC`
* IPv6 – `FF02::1:3`, Ethernet Multicast Adresse – `33-33-00-01-00-03`

Die Empfänger lauschen auch auf **TCP-Port 5355** auf der Unicast-Adresse, welche der Host zum Antworten nutzt. 