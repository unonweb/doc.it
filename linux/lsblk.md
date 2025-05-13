# lsblk

* https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/6/html/deployment_guide/s1-sysinfo-filesystems#s2-sysinfo-filesystems-lsblk

```sh
lsblk -o name,label,size,fstype,model

lsblk -o
lsblk --output <list> # Specify which output columns to print. Use --help to get a list of all supported columns.
lsblk -O
lsblk --output-all # Output all available columns.       

lsblk -f
lsblk --fs # Output info about filesystems; 
# equivalent to -o NAME,FSTYPE,FSVER,LABEL,UUID,FSAVAIL,FSUSE%,MOUNTPOINTS
lsblk -m
lsblk --perms # Output info about device owner, group and mode; 
# equivalent to -o NAME,SIZE,OWNER,GROUP,MODE
lsblk -t
lsblk --topology # Output info about block-device topology; 
# equivalent to -o NAME,ALIGNMENT,MIN-IO,OPT-IO,PHY-SEC,LOG-SEC,ROTA,SCHED,RQ-SIZE,RA,WSAME.

lsblk -P
lsblk --pairs # Produce output in the form of key="value" pairs.
```
