# iproute2

| Legacy utility   | Replacement command | Note                                |
| ---------------- | ------------------- | ----------------------------------- |
| ifconfig         | ip addr, ip link    | Address and link configuration      |
| route            | ip route            | Routing tables                      |
| arp              | ip neigh            | Neighbors                           |
| iptunnel         | ip tunnel           | Tunnels                             |
| nameif, ifrename | ip link set name    | Rename network interfaces           |
| ipmaddr          | ip maddr            | Multicast                           |
| netstat          | ss, ip route        | Show various networking statistics  |
| brctl            | bridge              | Handle bridge addresses and devices |

# ip

## temporary config vs. NetworkManager

Eine temporäre Interface-Konfiguration (z.B. mit ip) bleibt bei laufendem NetworkManager nicht lange erhalten, es sei denn das betreffende Interface wird aus der Verwaltung herausgenommen:  

```sh
nmcli device set interface managed no
```

## ip addr

```sh
ip a
ip addr
ip address
# mtu (maximum transfer unit der Netzwerkkarte)
# link/ether (MAC)
# dynamic (DHCP)
# scope (Reichweite)

ip a add <ip/mask> dev <interface>
ip a del <ip/mask> dev <interface>
# adds/deletes a secondary ip address to/from interface  
# If you add more than one address, your machine will accept packets for all of them. The first address you added will be used as source address for outgoing traffic by default, it's referred to as primary address.

ip a flush <interface> # entfernt sämtliche Konfiguration von interface
```

## ip link

network devices

```sh
ip l
ip link <cmd> <interface> <para>
ip link set <interface>
ip link set <interface> up # # bring interface up
ip link set <interface> down # bring interface down
ip link set <interface> name <name> # you can't rename an active interface
ip link set <interface> address <mac> # change link layer address (MAC)
ip link set <interface> promisc on|off
```

## ip neigh

```sh
ip neighbor
ip neigh

ip neighbor show # zeigt den ARP-Cache
ip neighbor add ip lladdr <link-layer-address> dev <interface>`  
# One of the use cases for it is to add static entry for an interface with disabled ARP to restrict interface usage only by hosts with specific MAC addresses.
```

## ip monitor

```sh
ip monitor # überwacht live die Zuordnung von MAC-Adressen zu IP-Adressen
```

## ip tunnel

IP-Tunnel (Einkapselung von IPv6-Paketen in IPv4-Pakete) überprüfen und konfigurieren

```sh
ip tunnel show # zeigt aktive IP-Tunnel
```

## ip route

```sh
ip r
ip route <cmd> <route> dev <interface>
ip route # alleine zeigt die Kernel-Routing-Tabelle  
# Die Ausgabe kann auch genauso als Kommando zur Definition der Route verwendet werden (nützlich beim Entfernen von route per copy/paste)
```

### files

```sh
cat /etc/iproute2/rt_protos # protocol Liste der reservierten Routing-Protokolle
cat /etc/iproute2/rt_tables # tables Liste der Routing-Tabellen
cat /etc/iproute2/rt_scopes # scope Liste der Scopes
```

### command

```sh
ip route list default
```



`add|del|change|append|replace`

### route

* `ip route add|change|replace`
* [`table <table>`] # default = main table; unless type = local, broadcast or nat, which are put into the local table
* `default` oder `to [type] prefix` 
  * `default` # special prefix; equivalent to IP 0/0 or to IPv6 ::/0.
  * default type = `unicast` 
  * prefix is an IP or IPv6 address optionally followed by a slash and the prefix length. If the length of the prefix is missing, ip assumes a full-length host route. 
* `via gateway` # address of the nexthop router
  * `default via gateway` # markiert alle Ziele, die nicht aufgrund anderer Routing-Einträge zuzuordnen sind
* [`scope scope`] # 
  * `global`    # default; for all gatewayed unicast routes
  * `link`      # for direct unicast and broadcast routes
  * `host`      # for local routes.
* `src <ip>`    # the source address to prefer when sending to the destinations covered by the route prefix
* `dev <interface>` # the output device name
* `onlink` # pretend that the nexthop is directly attached to this link, even if it does not match any interface prefix.
* `protocol <protocol>` # default = boot
  * `redirect`  # the route was installed due to an ICMP redirect.
  * `kernel`    # the route was installed by the kernel during autoconfiguration.
  * `boot`      # the route was installed during the bootup sequence; if a routing daemon starts, it will purge all of them.
  * `static`    # the route was installed by the administrator to override dynamic routing; routing daemon will respect them and, probably, even advertise them to its peers.
  * `ra`        # the route was installed by Router Discovery protocol.

#### examples

```sh
ip route add default via <ip> # setzt das Standardgateway auf <ip>; alle Pakete, die nicht aufgrund anderer Routing-Einträge zuzuordnen sind, werden an das Standard-Gateway geschickt.
ip route add default via <ip> dev <interface> # setzt das Standardgateway von <interface> auf <ip>
ip route add <ip/mask> via <ip> # fügt eine Route zu ip über (das Gateway) ip hinzu
```

# iw

```sh
iw list # lists all wireless devices and their capabilities
iw dev # zeigt alle verfügbaren WLAN-Interfaces mit Eigenschaften und Namen 
iw phy  # zeigt alle verfügbaren physikalischen WLAN-Interfaces mit ihren physikalischen Eigenschaften 
```

```sh
iw dev|phy <interface> <cmd>
```

`dev|phy` muss nicht spezifiziert werden, wenn der darauffolgende dev/phy-Name eindeutig ist

* = phy: hardware; layer 1
* = dev: interace; layer 2

```sh
iw  wlp8s0 link # Informationen zur Verbindung, Übertragungsrate und Signalstärke
# Signalstärke: Je höher der gemessene Wert (näher bei Null) ist, desto stärker ist das Signal
# -50 dBm: sehr gutes Signal
# -113 dBm = sehr schwaches Signal)
```

```sh
iw wlp8s0 info
```

```sh
sudo iw wlp8s0 scan
sudo iw wlp8s0 scan ssid internet # scan for wifi signals using wlp8s0 searching for ssid "internet"
```

```sh
iw wlp8s0 connect <SSID>
```

## set signal power

* `iw dev <interface> set txpower`
  * `fixed <n>` # fest eingestellter Wert
  * `auto`      # automatische Auswahl bis zum möglichen Maximalwert (ohne Angabe deines Wertes)
  * `limit`	    # Begrenzung auf einen Maximalwert, tatsächliche Leistung darf aber niedriger sein

## power management

* `iw dev interface get power_save`
* `iw dev interface set power_save on|off`

## region

```sh
iw reg get      # Ländereinstellung abfragen
iw reg set DE   # Ländereinstellung manuell ändern
```

## wake on wlan

* https://www.cyberciti.biz/faq/configure-wireless-wake-on-lan-for-linux-wifi-wowlan-card/

```sh
iw list | grep WoW -A10 # check support
iw phy0 wowlan show # check status
```
