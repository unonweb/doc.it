# traceroute / tracepath

**verschickt udp|tcp|icmp Pakete mit ansteigender TTL-Zahl (Hops)**

* nützlich, um langsame Router im Netzwerk zu identifizieren
* ebenso wie bei Ping können Informationen nur den Pongs entnommen werden

```sh
traceroute <ip>
traceroute -n # numerical; unterdrückt die Namensauflösung
traceroute -m
traceroute --max-hops <ttl> # maximum number of hops; default = 30
traceroute -M icmp|tcp|default # (=udp) specifies the method for traceroute operations

tracepath # does not require superuser privileges and has no fancy options
```
