# ping

verwendet **ICMP** (Internet Control Message Protocol); ein Ping muss immer mit **Pong** beantwortet werden; diesem Pong können dann Informationen entnommen werden (nicht dem Ping); 

Problem: ICMP-Pakete werden 

- oft nicht beantwortet (aber weitergeleitet) oder
- komplett gefiltert

```sh
ping 192.168.179.1
ping
ping6 <hostname>|<ip>
ping -c <count> # Anzahl der Versuche
ping -v # verbose
ping -I <interface> <ip>|<interface> # specifies the source
```
