# about

`/proc/sys` is where you can find all the information about devices, drivers, and some kernel features.

The actual internal structure of `/proc/sys` depends  heavily on the kernel being used, but you are likely to find the  following directories inside. In turn, each of them will contain other  subdirectories where the values for each parameter category are  maintained:

1. `dev`: parameters for specific devices connected to the machine
2. `fs`: filesystem configuration (quotas and inodes, for example)
3. kernel: kernel-specific configuration
4. `net`: network configuration
5. `vm`: use of the kernel’s virtual memory

# show

```sh
sudo sysctl -a  # shows the available kernel parameters
sudo sysctl -a | wc -l # shows the number of available kernel parameters
```

# temporary change

```sh
sudo sysctl net.ipv4.ip_forward # read
sudo sysctl -w net.ipv4.ip_forward=0 # write
```

# permanent change

```sh
sudo xed /etc/sysctl.conf
sudo nano /etc/sysctl.conf
```

A better and easier way to set individual runtime parameters is using **.conf** files inside `/etc/sysctl.d`, grouping them by categories

For example, instead of setting **net.ipv4.ip_forward=0** in **/etc/sysctl.conf**, we can create a new file named `net.conf` inside **/etc/sysctl.d**

# parameters

## fs

```sh
fs.file-max= 
# specifies the maximum number of file handles the kernel can allocate for the system. Depending on the intended use of your system (web / database / file server, to name a few examples),  you may want to change this value to meet the system’s needs. Otherwise, you will receive a “**Too many open files**” error message at best, and may prevent the operating system to boot at the worst.
```

## kernel

```sh
kernel.sysrq 
# is used to enable the SysRq key in your keyboard (also known as the print screen key) so as to allow certain key combinations to invoke emergency actions when the system has become unresponsive.
kernel.sysrq=16 
# The default value (16) indicates that the system will honor the Alt+SysRq+key combination and perform the actions listed in the sysrq.c documentation found in kernel.org (where key is one letter in the b-z range). For example, Alt+SysRq+b will reboot the system forcefully (use this as a last resort if your server is unresponsive).
```

## net.ipv4

### block ICMP requests?

Some network administrators disable ICMP and/or SNMP on network devices to improve security. Supporters of this position believe that it makes their network more secure by obscuring certain elements.

Disabling ping does not meaningfully increase security; it only makes troubleshooting and network monitoring much more difficult. If turning off ping increases network security, it only slightly slows down the most basic and unskilled cyber hacker. All other services such as HTTP(S), FTP, SMB, etc. are still running and likely discoverable.

### change

```ini
## DISABLE ICMP REDIRECT ACCEPTANCE ## 
net.ipv4.conf.all.secure_redirects = 0
net.ipv4.conf.default.secure_redirects = 0

## DISABLE ICMP REDIRECT SENDING ##
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0

net.ipv4.icmp_ignore_bogus_error_responses = 1 
# protection for bad icmp error messages
```

### defaults ubuntu 22.04

```ini
## FORWARDING
net.ipv4.ip_forward = 0
net.ipv4.conf.all.forwarding = 0
net.ipv4.conf.default.forwarding = 0
net.ipv6.conf.all.forwarding = 0
net.ipv6.conf.default.forwarding = 0

## REDIRECTS
# disable ICMP redirects acceptance
# ICMP redirects are important to routers, but can create security problems for servers:
net.ipv4.conf.all.accept_redirects = 0 
net.ipv4.conf.default.accept_redirects = 0 
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0

## SOURCE ROUTING

# tells netfilter to decline source routed packets; source routed packets are security risk, because they can can allow routing packets through an untrusted or insecure interface
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0
net.ipv6.conf.default.accept_source_route = 0

# ignore all ICMP, ECHO and TIMESTAMP requests sent to it via broadcast/multicast; avoid a smurf attack:
net.ipv4.icmp_echo_ignore_broadcasts = 1

## REVERSE PATH FILTER
# tries to ensure packets use legitimate source addresses; turned on it can prevent some IP spoofing attacks
# spoof protection
net.ipv4.conf.default.rp_filter = 2
net.ipv4.conf.all.rp_filter = 2
```

### syn flood protection

```ini
net.ipv4.tcp_syncookies = 1 
# sets the kernel to use the SYN cookies mechanism; can help to prevent SYN flood DDoS attacks by testing the validity of the SYN packets; the process is conducted without consuming memory or connection resources; SYN cookiesensure a server avoids dropping connections when the SYN queue fills up
net.ipv4.tcp_max_syn_backlog = 2048|`4096` 
# use a backlog queue size of *n* connections
net.ipv4.tcp_synack_retries = 2|3 
# the amount of time to keep half-open connections in the queue (3 equates to roughly 45 seconds).
sysctl net.ipv4.tcp_syn_retries = 2 #
```

### execshield

first introduced by Red Hat. The goal is to protect against stack/buffer/function pointer overflows. In most of the newer Linux distributions, the option to disable Exec Shield through sysctl was removed.

```sh
sudo dmesg | grep "Execute Disable" # checks if active
```

```ini
# Turn on execshield for reducing worm or other automated remote attacks 
kernel.exec-shield = 1
kernel.randomize_va_space = 1 
```
