---
tags:
  - firewall
---

[Ubuntu]; **uncomplicated firewall**; Entwickler: Canonical

a frontend for iptables and nftables which makes it much easier to configure these rules.

# config

```sh
sudo nano /etc/default/ufw
sudo nano /etc/ufw/ufw.conf 
# ENABLED=yes
# LOGLEVEL=low
```

# solutions

```sh
sudo ufw limit ssh/tcp # allows the connection but will deny connections if an IP address attempts to initiate 6 or more connections within 30 seconds
sudo ufw default deny incoming #
sudo ufw default allow outgoing #

sudo ufw allow 80/tcp # 
sudo ufw allow from 203.0.113.4 to any port 22
sudo ufw allow from 203.0.113.0/24 to any port 22 # subnetz
ufw allow from fe80::/64 # allow all link-local addresses
sudo ufw allow proto ipv6 from 2a02:3100:50ef:2400:c63b:33db:6e3:f449 to any port 27017

sudo ufw insert 7 deny from 2400:adc5:11f:5600:e468:9464:e881:b1c0 comment "unwanted hits on sdrforums.com"
```

# status

```sh
sudo ufw status numbered
sudo ufw status verbose
```

```sh
sudo ufw enable
sudo ufw disable
sudo ufw reload # reloads or enables/disables firewall on boot
```

# log

```sh
sudo ufw logging low # store logs related to blocked packets that do not match the current firewall rules and will show log entries related to logged rules.
sudo ufw logging medium # additionally you get logs for invalid packets, new connections, and logging done through rate limiting.
sudo ufw logging high
```

```sh
tail -f /var/log/ufw.log
```

```sh
journalctl -fk -g "UFW"
sudo journalctl -fk -g "UFW BLOCK" | egrep "$|DPT"
journalctl -fk -g "DST=192.168.178.98" # get all attempts to connect to my current ip
```

## add log rules

recommendation: turn your logging level `low` to have less clutter in logs and you can be more specific about the intentional monitoring

```sh
sudo ufw allow log service_name
sudo ufw allow log 22/tcp
```

## interpret logs

- `IN=ens33`: Shows the interface from which the packet has arrived.
- `OUT=`: For most of the users, this will not hold any value and if it is indicating any value, means there was an outgoing event.
- `LEN=60`: Shows the length of the packet (60 bytes in my case).
- `TOS=0x10`: Indicates the type of service.
- `PREC=0x00`: Shows "Precedence" type of service.
- `ID=4444`: Will give you a unique ID of the IP datagram and will be shared by the fragments of the same packets.
- `RES=0x00`: Indicates the reserved bits.
- `SYN URGP=0`: Here, the `SYN` flag indicates the request to make a new connection and `URGP=0` means the connection was not established.
- `ACK`: **The acknowledgment flag** is used to indicate that the packet is successfully received by the host.
- `PSH`: **The Push flag** indicates that the incoming data needs to be passed down to the application instead of getting buffed.
- `DF`: The "Do not fragment" flag of TCP.
# simple syntax

```sh
sudo ufw <action> <direction> <port>/<protocol> # if direction isn't supplied, the rule applies to incoming traffic
```

```sh
sudo ufw allow in 80/tcp
sudo ufw allow in 1022
ufw allow 22 
ufw allow ssh # # If the port you want to open or close is defined in */etc/services*, you can use the port name instead of the number (replace 22 with ssh); the protocol is automatically set.
ufw deny 22/tcp
ufw reject out 22
```

# complex syntax

```sh
sudo ufw allow in on wlp8s0 proto tcp from 192.168.178.0/24 to any port 8000:8050
sudo ufw allow in on wlp8s0 proto tcp from 192.168.178.0/24 to any port 4000:4050
```

* `proto <protocol>` 
* `from <ip> [port <port>]` # wird from definiert, muss auch ip definiert werden
* `to <ip> [port <port>]`   # wird to definiert, muss auch ip definiert werden

`ufw allow proto tcp from 192.168.0.2 to any port 22`
Grundsätzlich kann für protocol, ip und port auch immer *any* gesetzt werden

```sh
comment 'SSH port'
```

# ufw limit

```sh
ufw limit ssh/tcp # allows the connection but will deny connections if an IP address attempts to initiate 6 or more connections within 30 seconds
```

# ufw default

```sh
ufw default allow|deny|reject <direction> # change the default policy for traffic, where *direction* is one of incoming, outgoing or routed. Note that existing rules will have to be migrated manually when changing the default policy. im Gegensatz zu deny informiert reject den Absender
```

# ufw delete

There are two ways to delete UFW rules:

- By rule number
- By specification

```sh
sudo ufw status numbered
sudo ufw delete 4
sudo ufw delete allow 2222 # deletes the rule added by "sudo ufw allow 2222"

ufw delete <nr> # löscht die Regel mit der angegebenen Nr.
ufw reset # disables and resets firewall to installation defaults
```

## delete multiple rules

```sh
for i in {200..100}; do yes|sudo ufw delete $i; done
for i in {200..100}; do sudo ufw delete $i; done
```

# ??

```sh
-#payload ALL=NOPASSWD:/usr/bin/rsync
-borg ALL=NOPASSWD:SETENV:/usr/bin/borg
-#borg ALL=/usr/bin/rsync
```

