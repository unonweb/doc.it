# nmap

CLI-Portscanner; GUI frontend: **zenmap**

```sh
nmap [scantype] [options] <target>

nmap -O -sV ip # scan for hacking
```

# targets

```sh
nmap 192.168.1.* 
nmap 192.168.1.1-100
```

# default action

```sh
nmap <target> # (als root) führt zunächst eine host discovery durch (-PE -PS443 -PA80 -PP) und dann einen TCP SYN Scan (-sS) auf den 1000 meist genutzten Ports durch
```

1. **host discovery**: nmap sends an ICMP echo request, a TCP SYN packet to port 443, a TCP ACK packet to port 80, and an ICMP timestamp request (-PE -PS443 -PA80 -PP)
2. **port scan** against each host it determines is online;  

# host discovery

```sh
# list scan - no packets are sent to the target host (nmap can therefore not say whether the host is up or not), it just performs a host name lookup
nmap -sL
nmap -sL 192.168.179.*
nmap -sL 192.168.179.* | grep -i phone
```

```sh
# ping scan - only show available hosts that responded to the host discovery probes; no port scan
nmap -sn
nmap -sn 192.168.178.* # listet alle verfügbaren Hosts im Subnetz
```

```sh
nmap -sp 192.168.178.* # returns a list of hosts on your network and the total number of assigned IP addresses
```



```sh
nmap --traceroute # trace hop path to each host

nmap -n # skip DNS resolution
nmap -P # skip host discovery entirely, instead of treating hosts as though they are online regardless
```

# scanning techniques

```sh
sudo nmap -sS 192.168.178.* | less # TCP SYN scan (root-default)
# The most popular scan option. It can be performed quickly, scanning thousands of ports per second on a fast network not hampered by restrictive firewalls. It is also relatively unobtrusive and stealthy since it never completes TCP connections. It also allows clear, reliable differentiation between the open, closed, and filtered states.  
# This technique is often referred to as half-open scanning, because you don't open a full TCP connection. You send a SYN packet, as if you are going to open a real connection and then wait for a response. A SYN/ACK indicates the port is listening (open), while a RST (reset) is indicative of a non-listener. If no response is received after several retransmissions, the port is marked as filtered.

nmap -sT # TCP connect scan (regular-default)
# most basic form of TCP scanning; easily detectable as target machines are likely to log the connection

nmap -sY # SCTP INIT scan  
# SCTP is a relatively new alternative to the TCP and UDP protocols, combining most characteristics of TCP and UDP, and also adding new features like multi-homing and multi-streaming. It is mostly being used for SS7/SIGTRAN related services but has the potential to be used for other applications as well. SCTP INIT scan is the SCTP equivalent of a TCP SYN scan. It can be performed quickly, scanning thousands of ports per second on a fast network not hampered by restrictive firewalls. Like SYN scan, INIT scan is relatively unobtrusive and stealthy, since it never completes SCTP associations. It also allows clear, reliable differentiation between the open, closed, and filtered states.

nmap -sA # TCP ACK scan
# This scan is different than the others discussed so far in that it never determines open (or even open|filtered) ports. It is used to map out firewall rulesets, determining whether they are stateful or not and which ports are filtered.  
# The ACK scan probe packet has only the ACK flag set (unless you use --scanflags). When scanning unfiltered systems, open and closed ports will both return a RST packet. Nmap then labels them as unfiltered, meaning that they are reachable by the ACK packet, but whether they are open or closed is undetermined. Ports that don't respond, or send certain ICMP error messages back (type 3, code 0, 1, 2, 3, 9, 10, or 13), are labeled filtered.

nmap -sO # IP protocol scan
# IP protocol scan allows you to determine which IP protocols (TCP, ICMP, IGMP, etc.) are supported by target machines. This isn't technically a port scan, since it cycles through IP protocol numbers rather than TCP or UDP port numbers. Yet it still uses the -p option to select scanned protocol numbers, reports its results within the normal port table format, and even uses the same underlying scan engine as the true port scanning methods. So it is close enough to a port scan that it belongs here.

nmap -sW #
nmap -sM /Connect()/ACK/Window/Maimon scans
nmap -sU: UDP Scan
# While most popular services on the Internet run over the TCP protocol, UDP services are widely deployed. DNS, SNMP, and DHCP (registered ports 53, 161/162, and 67/68) are three of the most common. Because UDP scanning is generally slower and more difficult than TCP, some security auditors ignore these ports.  
# It can be combined with a TCP scan type such as SYN scan (`-sS`) to check both protocols during the same run.

nmap -sI zombie-host[:probeport] # idle scan
# This advanced scan method allows for a truly blind TCP port scan of the target (meaning no packets are sent to the target from your real IP address). Instead, a unique side-channel attack exploits predictable IP fragmentation ID sequence generation on the zombie host to glean information about the open ports on the target. IDS systems will display the scan as coming from the zombie machine you specify (which must be up and meet certain criteria).  
# Besides being extraordinarily stealthy (due to its blind nature), this scan type permits mapping out IP-based trust relationships between machines. The port listing shows open ports from the perspective of the zombie host. So you can try scanning a target using various zombies that you think might be trusted (via router/packet filter rules).  
# You can add a colon followed by a port number to the zombie host if you wish to probe a particular port on the zombie for IP ID changes. Otherwise Nmap will use the port it uses by default for TCP pings (80).
```

# output

```sh
nmap -v # increase verbosity level; use twice or more for even greater verbosity
nmap --open # only show open (or possibly open) ports
nmap --reason # display the reason a port is in a particular state
```

# service/version detection

```sh
nmap -A # enable OS detection, version detection, script scanning, and traceroute
nmap -sV # probe open ports to determine service/version info
nmaü -sV --version-intensity <level> # set from 0 (light) to 9 (try all probes)
nmap -sV --version-light ä limit to most likely probes (intensity 2)
nmap -sV --version-all # try every single probe (intensity 9)

nmap -O # enables OS detection
nmap --osscan-guess # guess OS more aggressively
```

# limit

```sh
nmap -p <port ranges> # only scan specified ports
nmap -p22
nmap -p1-65535
nmap -F # fast mode - scan fewer ports than the default scan
```

```sh
nmap -S <ip> # spoof source address
```

## 