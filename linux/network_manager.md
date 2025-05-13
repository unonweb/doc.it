# NETWORK MANAGER

```sh
systemctl status NetworkManager
```

# config

* https://networkmanager.pages.freedesktop.org/NetworkManager/NetworkManager/

nmcli keeps the changes in config files, so chances are we won’t have to edit any manually

## daemon config

* https://networkmanager.pages.freedesktop.org/NetworkManager/NetworkManager/NetworkManager.conf.html

```sh
man NetworkManager.conf
```

```sh
sudo nano /etc/NetworkManager/NetworkManager.conf # main config
ll /etc/NetworkManager/conf.d/ # additional config
```

```ini
[main]
dns=none
dns=dnsmasq
# use dnsmasq plugin
```

## connection config

* https://networkmanager.pages.freedesktop.org/NetworkManager/NetworkManager/settings-connection.html

## wireguard config

* https://networkmanager.dev/docs/api/latest/settings-wireguard.html

# nmcli

```sh
## output
nmcli -p
ncmli --pretty # output is pretty
nmcli -c
ncmli --colors yes # |no|auto
ncmli -t
ncmli --terse # terse output

nmcli -a
ncmli --ask # nmcli will stop and ask for any missing required arguments
```

## fields

* List available fields by providing an invalid value to the `--fields` option

```sh
nmcli --fields common # default
nmcli --fields all
nmcli --fields SIGNAL device wifi list
nmcli --fields NAME con # shows all connections
nmcli --fields NAME con show --active # ChateauSpezial
```

## general

```sh
nmcli general status
nmcli general hostname
nmcli general permissions
nmcli general logging 
```

## networking

```sh
nmcli networking on
nmcli networking off
nmcli networking connectivity # full
```

## radio

```sh
nmcli radio all # shows all radio status
nmcli radio wifi # enabled
nmcli radio wwan
nmcli radio wifi on
nmcli radio wifi off # show [or set] status of Wi-Fi 
```

## connection

connection profiles are saved here: `/etc/NetworkManager/system-connections/`

```sh
nmcli connection # shows connections
nmcli con # shows connections 

nmcli con show --active
nmcli con show ChateauSpezial

nmcli con down <con>
nmcli con monitor # prints a line whenever a connection changes
```

### add

```sh
nmcli connection add type wifi ssid Internet # add new
```

### edit

```sh
nmcli connection edit <id> # use an interactive editor tp edit an existing connection or add a new one
```

### up

```sh
nmcli connection up <id> # activates a connection; the connection is identified by its name or UUID
```

### import

```sh
nmcli connection import type wireguard file "/Pfad/zur/wg0.conf"
```

* cmds: `show|up|down|modify|add|edit|clone|delete|monitor|reload|load|import|export`
* `add`
  * `[save yes|no]` 
  * `type ethernet|wifi`
  * `con-name <name>`
  * `ip <ip>`
  * `gw <ip>`
  * `nmcli con add type ethernet con-name Myhome1 ifname enp0s3`
* `modify`  # if you modify a dhcp connection and change it to static don’t forget to change its ipv4.method from *auto* to *manual*
* `show`
  * `nmcli con show` # lists all connections
  * `nmcli con show --active` # lists all currently active connections

## device

Show and manage network interfaces

```sh
nmcli device
nmcli device status # the same; listet sämtliche Netzwerkgeräte inklusive Status auf; nicht verwaltet bedeutet, dass diese Schnittstelle noch über die Konfigurationsdatei */etc/network/interfaces* konfiguriert wird und dort auskommentiert werden muss.
```

```sh
nmcli dev set <device> managed yes
```

```sh
nmcli device wifi list # list available Wi-Fi access points, their signal strength
nmcli device wifi connect <ssid> # Connect to a Wi-Fi network specified by SSID
nmcli device wifi connect Gast
```

```sh
nmcli device show wlp8s0 # show all properties of the interface
nmcli device show wlp8s0 | grep IP4.DNS # gets the DNS server addresse associated with an interface
nmcli --fields CONNECTIONS device show wlp3s0 # shows all available connection profiles for your Wi-Fi interface wlp3s0
nmcli --fields GENERAL,WIFI-PROPERTIES device show wlan0 # shows details for wlan0 interface; only GENERAL and WIFI-PROPERTIES sections will be shown.
```

```sh
nmcli device monitor
```



* cmds: `status|show|set|connect|reapply|modify|disconnect|delete|monitor|wifi|lldp`
  * `set <interface>` # sets device properties
    * `autoconnect yes|no`
    * `managed yes|no` 