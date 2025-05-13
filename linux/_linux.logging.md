# RSYSLOG & JOURNALD

## differences

* **Journald**: originally designed for local logs on desktops – where there are not that many logs
* **rsyslog** was designed for high-performance central log collections from the ground up

**Rsyslog** can collect logs from many more sources, including pipes, sockets, and files. File sources are especially important, as many applications – like web servers – log to files and do that at a rate that **journald** cannot handle.

Both use **syslog** protocol. So you *could* use **rsyslog** and **journald** on the same machine without any problems.

The **rsyslog** modules `imjournal` (input) and `omjournal` output make it possible for rsyslog to read and write to the journal.

So if you write something to **rsyslog**, it will only appear in journald if you've configured the `omjournal` module.

## interaction

* [docs.redhat.com](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-viewing_and_managing_log_files#s1-interaction_of_rsyslog_and_journal)
* https://stackoverflow.com/a/73714497

## systemd mechanism

- A process calls `syslog()`
- --> writes to socket `/dev/log` = `/run/systemd/journal/dev-log`
- --> read by `systemd-journald` 
- --> which forwards to socket `/run/systemd/journal/syslog`
- --> which is read by `rsyslogd`

Hence any process calling `syslog()` actually writes into `/dev/log` synonymous of `/run/systemd/journal/dev-log`. 

```sh
ll /dev/log
lrwxrwxrwx 1 root root 28  5. Apr 18:36 /dev/log -> /run/systemd/journal/dev-log
```

As systemd-journald reads from this socket, this makes it capture the logs of all the processes writing into `/dev/log`. But systemd implements a mechanism to forward those logs to any "registered" service.

There is a `syslog.socket` unit which sets up the `/run/systemd/journal/syslog`:

```sh
[...]
[Socket]
ListenDatagram=/run/systemd/journal/syslog
[...]
```

The corresponding _syslog.service_ is triggered afterwards.

The configuration of `systemd-journald` decides if what is read from `/run/systemd/journal/dev-log` is forwarded or not to `/run/systemd/journal/syslog`:

```sh
cat /etc/systemd/journald.conf | grep ForwardToSyslog
#ForwardToSyslog=yes
```

The above commented line means that the default is **"yes"**.

# HOW TO

## **log** all interactive commands

… entered into the shell in a linux environment to a log file

### method 1

#### bash

```sh
sudo nano /etc/profile # add the following lines to the bottom of the file: 
```

```sh
# command line audit logging

function log2syslog {
	declare COMMAND
	COMMAND=$(fc -ln -0)
	logger -p local1.notice -t bash -i -- "${USER}:${COMMAND}"
}

trap log2syslog DEBUG

# the function *log2syslog* logs every command (catched by *fc -ln -0*) to the system log using *logger*
# the function is called by trap before every simple command 
```

#### rsyslog

```sh
sudo nano /etc/rsyslog.conf # add the following lines to the bottom of the file: 
```

```sh
# command line audit logging
local1.* -/var/log/cmdline
```

```sh
systemctl restart rsyslog #
```

### method 2

#### bash

```sh
sudo nano /etc/bash.bashrc # Append to the end of that file:

export PROMPT_COMMAND='RETRN_VAL=$?;logger -p local6.debug "$(whoami) [$$]: $(history 1 | sed "s/^[ ]*[0-9]\+[ ]*//" ) [$RETRN_VAL]"' #
```

#### rsyslog

```sh
sudo nano /etc/rsyslog.d/bash.conf

local6.*   /var/log/commands.log #

systemctl restart rsyslog #
```

#### logrotate

```sh
/etc/logrotate.d/rsyslog #

/var/log/mail.warn
/var/log/mail.err
# [...]
/var/log/message

# So add the new bash-commands log file in that list:
/var/log/commands.log
```

## log command output with script

```sh
script -qc "ls -l" output_log.txt # view stderr and write to file simultainiously
```

## other

```sh
less /var/log/messages #   
tail -f /var/log/messages # (following)

# apache
tail -f /var/log/apache2/access.log
tail -f /var/log/apache2/error.log

# ufw
sudo ufw logging low
sudo ufw logging high

tail -f /var/log/ufw.log
journalctl -fk -g "UFW"
journalctl -fk -g "DST=192.168.178.98" # get all attempts to connect to my current ip
```

# LOG FILES

## var/log/

```sh
ll -hS /var/log/ # zeigt die nach Größe sortierten Log-Dateien

/var/log/messages # global system messages, including the messages that are logged during system startup; includes mail, cron, daemon, kern, auth, etc.

/var/log/dmesg # kernel ring buffer information; view content of this file using dmesg.
/var/log/auth.log # system authorization information**, including user logins and authentication mechanism that were used.

/var/log/daemon.log # information logged by the various background daemons that runs on the system
/var/log/dpkg.log # information logged when a package is installed or removed using dpkg
/var/log/kern.log # information logged by the kernel. Helpful for you to troubleshoot a custom-built kernel
/var/log/user.log # information about all user level logs
```

## binary log files

```sh
/var/log/wtmp or /var/log/utmp # contains login records; use who or w to display this file
/var/log/lastlog # binary file; recent login information for all users; use lastlog to display this file.
/var/log/tallylog # binary file; log file of pam_tally2 (module that maintains a count of attempted accesses, resets count on success, denies access if too many attempts fail)
```
