Paket: **isc-dhcp-client** (Debian); **dhclient.x86_64** (RedHat); Entwickler: **ISC**

# CLI

```sh
dhclient -r # releases the current IP-lease and stops the running DHCP client

dhclient -x # stops the running DHCP client without releasing the current lease, kills existing dhclient process as previously recorded in the PID file

dhclient -1 # tries to get a lease once

dhclient -v # enable verbose log messages.
```



#### [interfaces]

Für welche Netzwerkschnittstellen soll der dhclient eine Konfiguration anfordern?

1. ​	ifupdown: /etc/network/interfaces bzw. /etc/sysconfig/network-scripts/ifcfg-interface
2. ​	netplan: /etc/netplan/01-netcfg.yaml
3. ​	NetworkManager

#### [config]

/etc/dhcp/dhclient.conf

unter Verwendung des NetworkManagers; wird die Konfiguration von dhclient dynamisch unter /var/lib/NetworkManager/ angelegt

**request** [option1, option2,…]; # causes the client to request (@ any server responding) the specified options; default (DHCPv4) includes subnet-mask, broadcast-address, time-offset, routers, domain-name, domain-search, domain-name-servers, host-name, nis-domain, nis-servers, ntp-servers and interface-mtu options.

**request;** # requests the default options

**also request optio****n;** # request *option* additionally to the defaults

**prepend** **domain-name-servers** *ip* # fügt (neben den automatisch bezogenen) weitere DNS-Server in der /etc/resolv.conf hinzu

**interface** "*interface*" { *declarations* ... } # used to indicate the interface on which the lease is valid. If set, this lease will only be tried on a particular interface; a client with more than one network interface may require different behaviour depending on which interface is being configured. All timing parameters and declarations other than lease and alias declarations can be enclosed in an interface declaration, and those parameters will then be used only for the interface that matches the specified name.

**fixed-address** *ip*; # used to set the ip address of a particular lease; required for all lease statements