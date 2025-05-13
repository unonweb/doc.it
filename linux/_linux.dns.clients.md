# dns clients 

* https://zwischenzugs.com/2018/06/08/anatomy-of-a-linux-dns-lookup-part-i/
* https://tailscale.com/blog/sisyphean-dns-client-linux

# nslookup

```sh
nslookup google.de # 
```

# /etc/nsswitch.conf

```sh
sudo fgrep "hosts:" /etc/nsswitch.conf 
# files mdns4_minimal [NOTFOUND=return] dns myhostname mymachines
```

1. The 'files' module searches /etc/hosts, then returns "not found".
2. Processing continues to the next module.
3. The 'mdns4_minimal' module searches the local LAN subnet using Multicast DNS (mDNS), then returns "not found".
4. `[NOTFOUND=return]` indicates that processing should not continue after this error; i.e. "not found" should be immediately returned to the program.
5. The 'dns' module is never reached.

Why the extra "[NOTFOUND=return]"? According to various sources, it's there to speed up unsuccessful queries and to prevent information  leakage, and to reduce load on public DNS servers.

Let's say someone's network *actually used* mDNS (which is  common on Linux/macOS). If the user tried to resolve  "MyLittleLaptop.local" and it wasn't found, the system would keep trying the next module ('dns'), and the query would be sent to the public DNS  (e.g. to the school's DNS servers, or to the coffee shop's router).

But – according to the reservation by IETF – `*.local`  names cannot possibly exist in public DNS, so such a query would be  useless, all it does is reveal your personal information to the network  admin. So the [NOTFOUND=return] tag is added to stop it from reaching  DNS completely.

If your corporate network uses `.local` for internal DNS and you're *fairly sure* it'll never use mDNS, you can remove the whole module – resulting in:

```ini
hosts: files dns
```

# /etc/resolv.conf

* is the file that configures the domain name resolver

**specifies the IP address of the domain name server that the resolver can query against**. We can configure up to a maximum of three different DNS by specifying the *nameserver* directive repeatedly. Then, the resolver will query the DNS according  to the order of the nameserver. 

In **Ubuntu** 18.04 und neuer existiert diese Datei gar nicht mehr, sondern sie ist eine symbolische Verknüpfung auf `/run/systemd/resolve/stub-resolv.conf`. Sie wird von `systemd-resolved` verwaltet und darf auf keinen Fall manuell editiert werden, solange ein Verwaltungsprogramm aktiv ist. In dieser Datei ist nur die IPv4-Adresse 127.0.0.53 als DNS-Server eingetragen. Dies ist der Dienst systemd-resolved, welcher auf dem TCP- und UDP-Port 53 auf der IPv4-Adresse 127.0.0.53 lauscht.

```sh
ll /etc/resolv.conf
# /etc/resolv.conf -> ../run/systemd/resolve/stub-resolv.conf
```

```sh
# cat /etc/resolv.conf
nameserver 127.0.0.53 # points to the systemd-resolved stub listener
options edns0 trust-ad
search fritz.box
```

# dnsmasq

* A lightweight DHCP and caching DNS server.

## cli

```sh
dnsmasq --test
```

## setup common

```sh
sudo apt install dnsmasq
systemctl status dnsmasq
sudo systemctl start dnsmasq
```

```sh
# disable the resolved service:
systemctl status systemd-resolved.service
sudo systemctl disable --now systemd-resolved

# remove the symlink /etc/resolv.conf
ls -lh /etc/resolv.conf # etc/resolv.conf -> ../run/systemd/resolve/stub-resolv.conf
sudo unlink /etc/resolv.conf

# disable network manager dns
sudo xed /etc/NetworkManager/NetworkManager.conf
# [main]
# dns=none
sudo systemctl restart NetworkManager.service
```

## setup 1

```sh
sudo xed /etc/dnsmasq.conf
sudo xed /etc/resolv.dnsmasq.conf
```



```sh
# create a new resolv.conf
echo nameserver 8.8.8.8 | sudo tee /etc/resolv.conf # google
echo nameserver 5.9.164.112 | sudo tee /etc/resolv.conf # digitalcourage
echo nameserver 1.1.1.1 | sudo tee /etc/resolv.conf # cloudflare

# edit config
sudo xed /etc/dnsmasq.conf
sudo systemctl restart dnsmasq
sudo systemctl enable dnsmasq.service
```

```ini
# /etc/dnsmasq.conf

# resolve all subdomains of localhost.local to 127.0.0.1. 
# If we visit any subdomain of localhost.com, such as whatever.localhost.com or 123456.localhost.com, it will resolve to 127.0.0.1.
address=/localhost.local/127.0.0.1

# Listen on this specific port instead of the standard DNS port (53). Setting this to zero completely disables DNS function,
# leaving only DHCP and/or TFTP.
port=53

domain-needed
bogus-priv
listen-address=127.0.0.1
expand-hosts
domain=dns-example.com
cache-size=1000
# Add other name servers here, with domain specs if they are for non-public domains
server=1.1.1.1

log-queries
```

## setup 2

```sh
echo nameserver 127.0.0.1 | sudo tee /etc/resolv.conf # localhost
```

```sh
sudo xed /etc/resolv.dnsmasq.conf
```

```ini
# IPv4 nameservers: 
nameserver 1.1.1.1
# IPv6 nameservers:
nameserver 2606:4700:4700::1111
nameserver 2606:4700:4700::1001
```

```sh
sudo xed /etc/dnsmasq.conf
```

```ini
log-queries
# Change this line if you want dns to get its upstream servers from somewhere other than /etc/resolv.conf
resolv-file=/etc/resolv.dnsmasq.conf

domain-needed
bogus-priv

#address=/localhost.local/127.0.0.1
address=/unonweb.local/127.0.0.1
listen-address=127.0.0.1
no-dhcp-interface=127.0.0.1
```

