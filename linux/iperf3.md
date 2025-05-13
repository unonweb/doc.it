# iperf3

**perform network throughput tests**

To perform an iperf3 test the user must establish both a server and a client

```sh
sudo apt install iperf3

iperf3 -c speedtest.wtnet.de -P
iperf3 -c speedtest.wtnet.de --parallel <n> #  number of parallel client streams to run
iperf3 -c speedtest.wtnet.de -4
iperf3 -c speedtest.wtnet.de --version4 # only use IPv4

iperf3 -c speedtest.wtnet.de -bidir # birektional
iperf3 -c speedtest.wtnet.de  -R
iperf3 -c speedtest.wtnet.de --reverse # reverse the direction of a test, so that the server sends data to the client
```

```sh
# Zur Verfügung stehende Ports sind 5200 bis 5209
iperf3 -c speedtest.wtnet.de -p 5200 -P 10 -4 # IPv4
iperf3 -c speedtest.wtnet.de -p 5209 -P 10 -6 # IPv6
# reverse mode (Server sendet, Client empfängt:
iperf3 -c speedtest.wtnet.de -p 5200 -P 10 -4 -R  # IPv4
iperf3 -c speedtest.wtnet.de -p 5209 -P 10 -6 -R # IPv6

# bidirektional
iperf3 -c speedtest.wtnet.de -p 5208 -4 -bidir
iperf3 -c speedtest.wtnet.de -p 5209 -4 -bidir

# port 5200
# 10 parellel requests
# only use IPv4
```
