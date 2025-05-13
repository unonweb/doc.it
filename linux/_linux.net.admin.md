## config [permanent]

ifupdown: Wenn `/etc/network/interfaces` und `/etc/network/interfaces.d./` leer sind, ist der Ifupdown-Mechanismus außer Kraft.

* Bei Ubuntu ersetzt *netplan* die Konfiguration über den ifupdown-Mechanismus

### netplan

* [netplan.io]("https://netplan.io/reference/")
* provides a network configuration abstraction over the currently supported two “backend” system, (or “renderer” in Netplan terminology): *networkd* and *NetworkManager*. 
  During early boot, the netplan “network renderer” runs which reads `/{lib,etc,run}/netplan/*.yaml` and writes configuration to `/run` to hand off control of devices to the specified networking daemon.
* Configured devices get handled by *systemd-networkd* by default, unless explicitly marked as managed by a specific renderer (*NetworkManager*)

#### commands

* `netplan try` # attempt to apply the new settings to a running system; if it fails, it will automatically revert to the previous working configuration; if it works, the new config. will be applied.

#### config files

There are three locations in which Netplan configuration files can be placed; in order of priority they are:

* `/run/netplan`
* `/etc/netplan`
* `/lib/netplan`

#### config

##### common properties for physical device types

* `match:` # selects a subset of available physical devices by various hardware properties. The following configuration will then apply to all matching devices, as soon as they appear. All specified properties must match.


```yaml
network:
  version: 2
  renderer: networkd|NetworkManager                # Use the given networking backend for this definition.
    match: # selects a subset of available physical devices by various hardware properties.
      name: enp2* # all cards on second PCI bus:
      macaddress: 11:22:33:AA:BB:FF # fixed MAC address:
wakeonlan: # Enable wake on LAN. Off by default.
```

### backend / network daemon

* *networkd* is normally used in server installations, where the network environment is fairly static.
* *NetworkManager* is normally used in desktop installations

#### networkd

* [linux-mag](https://www.linux-magazin.de/ausgaben/2014/08/networkd/)
  *systemd-networkd*; Ersatz für den ifupdown-Mechinsmus; Alternative zum NetworkManager;

* `systemctl status systemd-networkd.service`
* `systemctl start systemd-networkd.service`
* `networkctl`                      # show state of the network links as seen by systemd-networkd
* `networkctl status`               # gibt einen Überblick der Netzwerkkonfiguration (IP-Addr., Gateway, DNS, NTP)
* `networkctl status <interface>`   # gibt Informationen über das Interface (Driver, Vendor, Modell, MAC,…)

##### config files

Die Konfiguration wird i.d.R. durch netplan vorgenommen!

* `/lib/systemd/network/`   # Konfigurationsdateien
* `/run/systemd/network/`   # Konfiguration/Units zur Laufzeit; hier liegen durch netplan erzeugte .network-units
* `/etc/systemd/network/`   # eigene Konfiguration
* network file must have the extension *.network*
  `/etc/systemd/network/<Nummer>-<Bezeichnung>.network`

##### config

```ini
[Match]
MacAddrese=         # Die Hardware-Adresse der Schnittstelle.
Path=               #	Eine mit Leerzeichen getrennte Liste der udev-Eigenschaft "ID_PATH".
Type=               #	Eine mit Leerzeichen getrennte Liste der udev-Eigenschaft "DEVTYPE".
Name=enp2s0         # Eine mit Leerzeichen getrennte Liste der udev-Eigenschaft "INTERFACE". 
[NETWORK]
Description=             # Beschreibung der Schnittstelle. Dies dient nur der Darstellung.
DHCP=yes|no|ipv4|ipv6
Address=192.168.1.2/24  # statische ipv4/ipv6-Adresse und ihrer Prefix-Länge.
Gateway=                 # Mehrfachangabe dieser Option ist möglich. Mehr Optionen bietet die Sektion `[Route]`.
DNS=             # DNS-Server für die Namensauflösung. Mehrfachangabe möglich. *systemd-resolved.service* nutzt diesen Wert.
NTP=             #	NTP-Serveradresse. Mehrfachangabe möglich. *systemd-timesyncd.service* nutzt diesen Wert.
DHCPServer=yes|no   # If set to "yes", DHCPv4 server will be started. 
```

`systemctl restart systemd-networkd`

#### NetworkManager

##### files

* `/etc/NetworkManager` # upper case!!
* `/etc/NetworkManager/system-connections/` # connection profiles