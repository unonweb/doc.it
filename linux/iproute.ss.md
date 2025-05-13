# ss

**socket statistics**; similar to `netstat`; can display more TCP and state informations than other tools.

Quelle: Netlink Protokoll (wird verwendet, um Informationen zwischen Kernel- und User-Space-Prozessen zu übertragen)

```sh
ss # shows a list of open non-listening sockets with established connections
ss -tul # zeigt alle offenen tcp/udp Ports
ss -tulp # zeigt die dahinter stehenden Prozesse

ss -t state connected | grep --invert-match 127.0.0.1 # show current remote tcp connections
```

## socket state

```sh
ss -a
ss --all # shows both listening and non-listening  (for TCP this means established connections) sockets.
ss -l
ss --listening # shows only listening sockets (omitted by default)
```

## output

```sh
ss -n
ss --numeric # don't resolve service names
ss -p
ss --processes # shows process using socket.
ss -s
ss --summary # shows summary statistics (independently of the protocols selected)
ss -e
ss --extended # shows detailed socket information
```

## protocol/socket family

```sh
ss -t
ss --tcp # shows only tcp sockets
ss -u 
ss --udp # shows only udp sockets
ss -x 
ss --unix # shows only unix sockets
ss -f
ss --family unix|inet|inet6|link
```

## state filter

```sh
ss state established
# All standard TCP states: established, syn-sent, syn-recv, fin-wait-1, fin-wait-2, time-wait, closed, close-wait, last-ack, listening and closing.
ss state connected # all the states except for listening and closed
```

## filter

```sh
# filter by host
dst = HOST # Test if the destination or source matches HOST
src = HOST 

# host syntax is [FAMILY:]ADDRESS[:PORT]
ss state connected dst = inet:127.0.0.1 # show only localhost connections

# filter by port number
ss dst :5228
ss -a dst :5228 or src :mysql
ss -at '( dport = :22 or sport = :22 )'
```
