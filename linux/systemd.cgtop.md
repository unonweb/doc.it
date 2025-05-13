**Show top control groups by their resource usage**

shows the top control groups of the local Linux control group hierarchy, ordered by their CPU, memory, or disk I/O load. The display is refreshed in regular intervals (by default every 1s), similar in style to top. If a control group path is specified, shows only the services of the specified control group.

```sh
systemd-cgtop --iterations= # Perform only this many iterations. A value of 0 indicates that the program should run indefinitely; -n
systemd-cgtop -1 # A shortcut for --iterations=1

systemd-cgtop --order=path # Order by control group path name.
systemd-cgtop --order=tasks # Order by number of tasks/processes in the control group.
systemd-cgtop --order=cpu # Order by CPU load.
systemd-cgtop --order=memory # Order by memory usage
```

