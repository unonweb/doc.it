# Links

* https://github.com/pirate/wireguard-docs
* https://gist.github.com/4np/a2ba4631c1cfe7326ecc6dbc82b95dec
* https://www.stavros.io/posts/how-to-configure-wireguard/
* https://www.procustodibus.com/blog/2021/01/how-to-monitor-wireguard-activity/

# BASICS

WireGuard securely encapsulates IP packets over UDP. You add a WireGuard interface, configure it with your private key and your peers' public  keys, and then you send packets across it. 

All issues of key distribution and pushed configurations are *out of scope* of WireGuard

## simple network interface

WireGuard associates tunnel IP addresses with public keys and remote  endpoints. When the interface sends a packet to a peer, it does the  following:

1. This packet is meant for 192.168.30.8. Which peer is that? Let me look... Okay, it's for peer `ABCDEFGH`. (Or if it's not for any configured peer, drop the packet.)
2. Encrypt entire IP packet using peer `ABCDEFGH`'s public key.
3. What is the remote endpoint of peer `ABCDEFGH`? Let me look... Okay, the endpoint is UDP port 53133 on host 216.58.211.110.
4. Send encrypted bytes from step 2 over the Internet to 216.58.211.110:53133 using UDP.

When the interface receives a packet, this happens:

1. I just got a packet from UDP port 7361 on host 98.139.183.24. Let's decrypt it!
2. It decrypted and authenticated properly for peer `LMNOPQRS`. Okay, let's remember that peer `LMNOPQRS`'s most recent Internet endpoint is 98.139.183.24:7361 using UDP.
3. Once decrypted, the plain-text packet is from 192.168.43.89. Is peer `LMNOPQRS` allowed to be sending us packets as 192.168.43.89?
4. If so, accept the packet on the interface. If not, drop it.

## IP

* IPv4 pool: **10.8.0.0/24**
* private tunnel IPv4: **10.8.0.1/24**

# SETUP

### wg genkey

#### server

```sh
wg genkey | sudo tee /etc/wireguard/private.key
sudo chmod go= /etc/wireguard/private.key

sudo cat /etc/wireguard/private.key | wg pubkey | sudo tee /etc/wireguard/public.key
```

#### peer

* create client keys on server and then copy them to client

```sh
sudo mkdir -p /etc/wireguard/peers/iphone # create a new directory to hold your peer config
wg genkey | sudo tee /etc/wireguard/peers/iphone/private.key
sudo chmod go= /etc/wireguard/peers/iphone/private.key

sudo cat /etc/wireguard/peers/iphone/private.key | wg pubkey | sudo tee /etc/wireguard/peers/iphone/public.key
```

# HOW TO

## just a single connection

If you just want a single connection between two computers (say, to  connect your laptop to your home server), the configuration is pretty simple. 

On the server:

```sh
# /etc/wireguard/wg_single.conf

[Interface]
Address = 192.168.2.1
PrivateKey = <server's privatekey>
ListenPort = 51820

[Peer]
PublicKey = <client's publickey>
AllowedIPs = 192.168.2.2/32
```

On the client:

```sh
# /etc/wireguard/wg_single.conf

[Interface]
Address = 192.168.2.2
PrivateKey = <client's privatekey>
ListenPort = 21841

[Peer]
PublicKey = <server's publickey>
Endpoint = <server's ip>:51820
AllowedIPs = 192.168.2.0/24

# This is for if you're behind a NAT and
# want the connection to be kept alive.
PersistentKeepalive = 25
```

```sh
wg-quick up wg_single # on the server and then on the client
```

The two machines should now be connected.

## routing

### disable local network access

```ini
PostUp = iptables -I OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL -j REJECT && ip6tables -I OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL -j REJECT

PreDown = iptables -D OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL -j REJECT && ip6tables -D OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL -j REJECT
```

### enable local network access

includes an exception for the local network

```ini
PostUp = iptables -I OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL ! -d 192.168.1.0/24 -j REJECT && ip6tables -I OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL -j REJECT

PreDown = iptables -D OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL ! -d 192.168.1.0/24 -j REJECT && ip6tables -D OUTPUT ! -o %i -m mark ! --mark $(wg show %i fwmark) -m addrtype ! --dst-type LOCAL -j REJECT
```

# CONFIG

* the maximum length of a Linux interface name is **15 characters**

```sh
sudo nano /etc/wireguard/wg0.conf
wg-quick up /etc/wireguard/wg0.conf # pass path to config
```

## directives

* https://www.procustodibus.com/blog/2021/01/wireguard-endpoints-and-ip-addresses/

`Address` field tells WireGuard is two things:  

- What your computer's IP is on the WireGuard interface. This is just the IP address without the subnet mask.  
- What IP addresses WireGuard should handle. This is the entire subnet.  

Be sure to keep this in sync with the other side's `AllowedIPs` setting for you. So, for example, if you change your address to `172.16.4.4` but the other side only accepts IP packets with source=`10.6.9.4` (as per `AllowedIPs` setting) from you then the other side's Wireguard instance would ignore your packets. `AllowedIPs` specifies what source addresses can be used *on the tunnel* by the other peer. This is also going to be used for the routing that  happens inside Wireguard (selecting a peer based on the destination  address of a packet and the `AllowedIPs`).  

Like with other network interfaces, the IP address for a WireGuard  interface is defined with a **network prefix**, which tells the local host what other IP addresses are available on the same virtual subnet as the  interface. In the above example, this prefix is `/32` (which generally is a safe default for a WireGuard interface). If we set it to `/24`, that would indicate to the local host that other addresses in the same `/24` block as the address itself (`10.0.0.0` to `10.0.0.255`) are routable through the interface.

### [Peer]

`AllowedIPs` # 

*  a list of addresses that will get routed to the peer
*  defines the IP ranges for which a peer will route traffic
*  https://techoverflow.net/2021/07/09/what-does-wireguard-allowedips-actually-do/

```sh
AllowedIPs = 192.0.2.3/32 # peer is a simple client that only accepts traffic to/from itself
AllowedIPs 0.0.0.0/0 # to indicate a default route to send all internet and VPN traffic through that peer
# ::/0 # for IPv6
```

The **fundamental principle** of a secure VPN is an association between peers and the IP addresses each is allowed to use as source IPs.

```sh
AllowedIPs = 10.82.85.2/32, 192.168.200.0/24
```

* It adds a route to the given networks
  i.e. packets with the given **destination IPs** will be routed through the WireGuard interface to that peer
* It will allow packets with the given **source IPs** to be routed from the given peer on the WireGuard interface

## server

```sh
# sudo nano /etc/wireguard/wg0.conf

[Interface]
PrivateKey = base64_encoded_private_key_goes_here
Address = 10.8.0.1/24, fd0d:86fa:c3bc::1/64
ListenPort = 51820
SaveConfig = true

[Peer]
PublicKey = <FooPhone public key>
AllowedIPs = 10.8.0.2/32 # must match the Address field of the peer
```

### enable routing

* https://www.digitalocean.com/community/tutorials/how-to-set-up-wireguard-on-ubuntu-20-04

If you are using WireGuard to connect a peer to the WireGuard Server in order to access services on the **server only**, then you do not need to complete this section. If you would like to  route your WireGuard Peer’s Internet traffic through the WireGuard  Server then you will need to configure IP forwarding by following this  section of the tutorial.

```sh
sudo nano /etc/sysctl.conf
net.ipv4.ip_forward=1

sudo sysctl -p # read the file and load the new values for your current terminal session
```

Now your WireGuard Server will be able to forward incoming traffic from  the virtual VPN  ethernet device to others on the server, and from there to the public Internet. Using this configuration will allow you to  route all web traffic from your WireGuard Peer via your server’s IP  address, and your client’s public IP address will be effectively hidden.

```bash
ip route list default
# default via 203.0.113.1 dev eth0 proto static
# The public interface is the string found within this command’s output that follows the word “dev”. 
```

```bash
sudo nano /etc/wireguard/wg0.conf

PostUp = ufw route allow in on wg0 out on eno1
PostUp = iptables -t nat -I POSTROUTING -o eno1 -j MASQUERADE
PreDown = ufw route delete allow in on wg0 out on eno1
PreDown = iptables -t nat -D POSTROUTING -o eno1 -j MASQUERADE
```

- `ufw route allow in on wg0 out on eth0` - This rule will allow forwarding IPv4 and IPv6 traffic that comes in on the `wg0` VPN interface to the `eth0` network interface on the server. It works in conjunction with the `net.ipv4.ip_forward` and `net.ipv6.conf.all.forwarding` sysctl values that you configured in the previous section.
- `iptables -t nat -I POSTROUTING -o eth0 -j MASQUERADE` - This rule configures masquerading, and rewrites IPv4 traffic that comes in on the `wg0` VPN interface to make it appear like it originates directly from the WireGuard Server’s public IPv4 address.
- `ip6tables -t nat -I POSTROUTING -o eth0 -j MASQUERADE` - This rule configures masquerading, and rewrites IPv6 traffic that comes in on the `wg0` VPN interface to make it appear like it originates directly from the WireGuard Server’s public IPv6 address.

### open ports

```bash
sudo ufw allow 51820/udp
sudo ufw allow OpenSSH
```

If you decide to tunnel all of your network traffic over the VPN connection, you will need to ensure that port `53` traffic is allowed for DNS requests, and ports like `80` and `443` for HTTP and HTTPS traffic respectively. If there are other protocols  that you are using over the VPN then you will need to add rules for them as well.

```sh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 53
```

```sh
# reload ufw
sudo ufw disable
sudo ufw enable
```

## peer

* `AllowedIPs = 0.0.0.0/0` # send all your peer’s traffic over the VPN and use the WireGuard Server as a gateway for all traffic
* `AllowedIPs = 10.8.0.0/24` # only send traffic over the VPN if the destination system has an IP address in this range

```sh
# /etc/wireguard/peers/iphone/iphone.conf

[Interface]
PrivateKey = <FooPhone private key>
Address = 10.8.0.2/24 # can be anything in the subnet as long as it is different from the server’s IP
DNS = 1.1.1.1, 1.0.0.1

[Peer]
PublicKey = <Linux peer public key>
AllowedIPs = 10.8.0.0/24 # instruct the peer to only send traffic over the VPN if the destination system has an IP address in either range. Using the AllowedIPs directive, you can restrict the VPN on the peer to only connect to other peers and services on the VPN, or you can configure the setting to tunnel all traffic over the VPN and use the WireGuard Server as a gateway.
Endpoint = 203.0.113.1:51820
PersistentKeepalive = 30
```

```sh
# generate a QR code to pass config easily to peer
qrencode -t ansiutf8 -r /etc/wireguard/peers/iphone/iphone.conf
```

### closed tunnel

**configure a peer to route all traffic over the tunnel**

If you have opted to route all of the peer’s traffic over the tunnel using the `AllowedIPs = 0.0.0.0/0` or `::/0` routes and the peer is a remote system, follow these instructions:

For remote peers that you access via SSH or some other protocol using a  public IP address, you will need to add some extra rules to the peer’s `wg0.conf` file. These rules will ensure that you can still connect to the system  from outside of the tunnel when it is connected. Otherwise, when the  tunnel is established, all traffic that would normally be handled on the public network interface will not be routed correctly to bypass the `wg0` tunnel interface, leading to an inaccessible remote system.

```bash
ip route list table main default
# default via 203.0.113.1 dev eth0 proto static
```

```bash
# sudo nano /etc/wireguard/wg0.conf
# 203.0.113.5 is assumed as public ip
PostUp = ip rule add table 200 from 203.0.113.5
PostUp = ip route add table 200 default via 203.0.113.1
PreDown = ip rule delete table 200 from 203.0.113.5
PreDown = ip route delete table 200 default via 203.0.113.1
```

These lines will create a custom routing rule, and add a custom route to ensure that public traffic to the system uses the default gateway.

- `PostUp = ip rule add table 200 from 203.0.113.5` - This command creates a rule that checks for any routing entries in the table numbered `200` when the IP matches the system’s public `203.0.113.5` address.
- `PostUp = ip route add table 200 default via 203.0.113.1` - This command ensures that any traffic being processed by the `200` table will use the `203.0.113.1` gateway for routing, instead of the WireGuard interface.

The `PreDown` lines remove the custom rule and route when the tunnel is shutdown.

# CLI

## wg-quick

* a simple script that wraps invocations to `wg` and `ip` in order to set up a WireGuard interface
* the configuration files for `wg-quick` aren’t compatible with the `wg` executable
* the maximum length of a Linux interface name is **15 characters**

```sh
wg-quick up|down <config-file> # <config-file> is  a configuration file, whose filename is the interface name followed by `.conf'
wg-quick up|down <interface> # <interface> is an interface name, with configuration found at /etc/wireguard/INTERFACE.conf
```

`wg-quick up`

* adds a WireGuard interface
* brings up the interface with the supplied IP addresses
* sets up mtu and routes
* optionally runs pre/post up scripts

## wg

- configuration utility for getting and setting the configuration of WireGuard tunnel interfaces
- The interfaces themselves can be added and removed using `ip-link` and their IP addresses and routing tables can be set using `ip-address` and `ip-route`

```sh
sudo wg show # shows the current configuration and device information
```

### wg genkey

**server:**

```sh
wg genkey | sudo tee /etc/wireguard/private.key
sudo chmod go= /etc/wireguard/private.key

sudo cat /etc/wireguard/private.key | wg pubkey | sudo tee /etc/wireguard/public.key
```

**peer**:

* create client keys on server and then copy them to client

```sh
sudo mkdir -p /etc/wireguard/peers/iphone # create a new directory to hold your peer config
wg genkey | sudo tee /etc/wireguard/peers/iphone/private.key
sudo chmod go= /etc/wireguard/peers/iphone/private.key

sudo cat /etc/wireguard/peers/iphone/private.key | wg pubkey | sudo tee /etc/wireguard/peers/iphone/public.key
```

## service

```bash
# configure wireguard to run as a systemd service:
sudo systemctl enable wg-quick@wg0.service # after @ pass name of config in /etc/wireguard/
sudo systemctl start wg-quick@wg0.service

wg-quick up /etc/wireguard/wg0.conf # pass path to config
```

# CONNECTION

```sh
nmcli connection import type wireguard file "/Pfad/zur/wg0.conf"
```

# TROUBLES

* ISSUE: **resolvconf: command not found**

  BACKGROUND: Modern Linux distributions use systemd, which comes with its own flavor of `resolvconf` as part of the `resolvectl` command. 

  SOLUTION 1: A symlink called `resolvconf` can be created somewhere in `$PATH`.

  On Debian (and Ubuntu?) you must do this manually. Using `/usr/local` to prevent possible package conflicts:

  ```sh
  ln -s /usr/bin/resolvectl /usr/local/bin/resolvconf
  ```

  SOLUTION 2: comment out the DNS in the wg0 conf file

  wg-quick does a bunch of things behind the scenes, one of them is  adjusting the nameserver, typically to use your vpn providers. Set your  /etc/resolv.conf to use Cloudflare (1.1.1.1), then try again. It should  connect without the resolvconf dependency. 









```sh
3CX Smarthpones in dasselbe Netz wie Desk-Phones
evtl. eingehende Ports freigeben
Install Nextcloud Tasks
Change Ansible-Vault PW
Implement File Permission Check Script
```

