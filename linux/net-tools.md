# net-tools

```sh
sudo apt install net-tools
```

* arp
* ifconfig
* netstat
* rarp
* nameif
* route

## route

**route** ohne Optionen, zeigt die Routing-Tabelle für alle Interfaces

```sh
route -A [inet6|inet] # use the specified address family
route -n # zeigt nummerische Adressen, keine Namensauflösung
route -C # zeigt den Routing Cache des Kernel und damit auch Routen, die dynamisch angelegt wurden
```