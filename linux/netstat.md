# netstat

```sh
netstat -tunlp # 
netstat -t # Show TCP ports.
netstat -u # Show UDP ports.
netstat -n # Show numerical addresses instead of resolving hosts.
netstat -l # Show only listening ports.
netstat -p # Show the PID and name of the listener’s process. This information is shown only if you run the command as root or sudo user.
```

```sh
# get process behind port
sudo netstat -tnlp | grep 3000
ps -Fp 3814 # -F extra full format; -p <pid>

netstat -tnlp | grep 3000 # 40195/node
kill 40195
```
