# arp

**manipulate the system ARP cache**

```sh
arp # ohne Optionen zeigt den ARP-Cache (/etc/ethers)
arp -d
apr --delete <ip> # löscht den Cache Eintrag für den Host <ip>
arp -s
arp --set <host> <hwaddr> # set a new ARP entry
```
