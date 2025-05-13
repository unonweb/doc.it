# Monit

As a **daemon**, Monit runs in cycles; It monitor services, then goes to sleep for a configured period, then wakes up and start monitoring again in an endless loop.

**Monit vs Nagios, Icinga, etc.**

While *Nagios*, *Icinga* and *check_mk* needs a monitoring server which connects in given time periods to the machines it checks, monit doesn’t need this kind of server in order to do these checks. ***M******onit checks locally and reports the results via mail***.

**Monit & Ansible**

In combination with a configuration management like Ansible you have an easy central to use monitoring tool with automatically distributed configuration files.

# CLI

## daemon

```sh
sudo systemctl start monit #
```

```sh
sudo monit <args> # connect to the Monit daemon (on TCP port 127.0.0.1:2812 by default) and 
# ask it to perform the requested action.
sudo monit #  starts the Monit daemon
sudo monit summary #
sudo monit -t # test the control file for syntax errors
sudo monit -d seconds # Run Monit as a daemon once every seconds (set daemon in monitrc)
sudo monit reload # Reinitialise a running Monit daemon, the daemon will reread its configuration, close and reopen log files.
sudo monit quit # Kill the Monit daemon process
```

## services

```sh
monit start|stop|restart|monitor|unmonitor all # Start all services listed in the control file and enable monitoring for them. 
# If the group option is set (-g), only start and enable monitoring of services in the named group ("all" is not required in this case).
monit start|stop|restart|monitor|unmonitor <name>  # Start the named service and enable monitoring for it.
# The name is a service entry name from the monitrc file.

monit status <name> # Print service status information.
monit summary <name> # Print a short status summary.

monit report up|down|initialising|unmonitored|total # Report services state. The output can easily be parsed by scripts.
# Without options, prints a short overview of the state of all services managed by Monit.
# The option, up prints the number of all services in this state, down likewise and so on.

monit validate # Check all services listed in the control file.
# This action is also the default behaviour when Monit runs in daemon mode.
monit procmatch regex # Allows for easy testing of pattern for process match check. The command takes regular expression as an argument and displays all running processes matching the pattern.
```

# Config

* https://www.mmonit.com/monit/documentation/monit.html
* https://wiki.ubuntuusers.de/Monit/

```sh
 ~/.monitrc # default location
sudo nano /etc/monit/monitrc # control file; main configuration file
ll /etc/monit/conf.d/
ll /etc/monit/conf-enabled/
```

* To protect the security of your control file and passwords the control file must have read-write permissions no more than **0700**; otherwise Monit will complain and exit.
* The control file consists of a series of ***service entries and global option statements***.

## syntax

* free-format, ***token-oriented syntax***
* keywords are **case insensitiv**

You can use noise keywords like 'if', 'and', 'with', 'only', 'then', 'for', 'of' anywhere in an entry to make it resemble English. They're ignored, but can make entries much easier to read at a glance. 

There are three kinds of tokens: ***grammar***, ***numbers*** and ***strings***.

```sh
IF EXIST THEN <action> # Triggers if the object does exist. 
# It can be used for example to kill a process which shouldn't be running.
```

```sh
# kill a process that should not run:
check process vmware matching "vmware"
if exist then exec "/ usr/bin/pkill -9 vmware"

# alert if a file exist which shouldn't:
check file x.sh with path /some/path/x
if exist then alert

if not exist then <action> # Triggers if the object does not exist. It can be used for example to make sure apache is running, data filesystem is mounted, etc.

check filesystem disk1 with path /dev/sda1
if does not exist then exec "/sbin/mount…"
```

