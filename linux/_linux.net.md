# solutions

### get process behind port

```sh
sudo netstat -tunlp | grep 3000
ps -Fp 3814

netstat -tnlp | grep 3000 # 40195/node
kill 40195
```

### get public ip

```sh
# dig
dig +short myip.opendns.com @resolver1.opendns.com
# curl
curl ifconfig.me
```

```sh
# get info about ip
curl "ipinfo.io/46.5.47.142?token=ebae6d28569d90"
```

### lsof

```sh
lsof -n -i:80 # see a specific port such as 22
-n # numeric; inhibits the conversion of network numbers to host names for network files
-i <addr> # selects the listing of files any of whose Internet address matches the address specified
```
