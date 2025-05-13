
### Protocol
**D**iscover  
**O**ffer  
**R**equest  
**A**cknowledge  

- **DHCPDISCOVER**: Client such per Broadcast nach einem DHCP-Server; er fügt dabei sein MAC-Adresse an; Zielport: 67; Quellport: 68
- **DHCPOFFER**: Server antwortet mit einem Konfigurationsangebot (auch per Broadcast, da der Client ja noch keine IP-Adresse hat); zur Identifikation des anfragenden Clients dient die MAC-Adresse
- **DHCPREQUEST**: Client forder die ihm angebotene Konfiguration explizit an; wenn ein Client eine Konfig. neu anfordert, überspringt er die beiden vorhergehenden Schritte und verschickt direkt einen REQUEST.
- **DHCPACK/DHCPNACK**: Server bestätigt die Konfiguration und reserviert die IP-Adresse für den Client.