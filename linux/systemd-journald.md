# JOURNALD

- https://www.freedesktop.org/software/systemd/man/latest/systemd-journald.service.html

systemd-journald is a system service that collects and stores logging data. It creates and maintains structured, indexed journals based on logging information that is received from a variety of sources:

- Kernel log messages, via **`kmsg`**
- Simple system log messages, via the libc **`syslog`** call
- Structured system log messages via the native Journal API, see **`sd_journal_print`** and Native Journal Protocol
- stdout & stderr of service units.
- Audit records, originating from the kernel audit subsystem



```sh
sudo systemctl status systemd-journald.service
```

## interaction with rsyslog

- [[_linux.logging]]

# FILES

- Logdateien liegen als Binärdaten vor
- können mit journalctl angezeigt werden
- `/var/log/journal/<machine-id>`

```sh
ll /var/log/journal # + machine id
du -sh /var/log/journal/ # size
```

# CONFIG

- https://www.freedesktop.org/software/systemd/man/latest/journald.conf.html#Options

```sh
sudo nano /etc/systemd/journald.conf
sudo xed /etc/systemd/journald.conf
```

```sh
Storage=volatile
# data will be stored only in memory --> /run/log/journal
Storage=persistent
# data will be stored preferably on disk --> /var/log/journal
Storage=auto 
# behaves like "persistent" if /var/log/journal directory exists, 
# behaves like "volatile" otherwise (the existence of the directory controls the storage mode)
```

```ini
Compress=yes

SplitMode=none
# journal files are not split up by user and all messages are instead stored in the single system journal
SplitMode=uid
# default; all regular users (with UID outside the range of system users, dynamic service users, and the nobody user) will each get their own journal files, and system users will log to the system journal

MaxFileSec=12354
# legt die max. Zeit fest, die ein einzelner Journaleintrag gespeichert werden soll
```

## disk usage

```ini
SystemMaxUse = 10%
SystemMaxUse = 4GB
RuntimeMaxUse = 10% 
# control how much disk space the journal may use up at most
# defaults to 10% of the size of the respective file system, but is capped to 4G
SystemKeepFree = 15%
RuntimeKeepFree = 15%
# control how much disk space systemd-journald shall leave free for other uses
# defaults to 15%, but is capped to 4G

SystemMaxFileSize = 500MB
RuntimeMaxFileSize = 
# control how large individual journal files may grow at most. This influences the granularity in which disk space is made available through rotation, i.e. deletion of historic data. Defaults to one eighth of the values configured with SystemMaxUse= and RuntimeMaxUse=, so that usually seven rotated journal files are kept as history. If the journal compact mode is enabled (enabled by default), the maximum file size is capped to 4G.
```

## forwarding

```ini
ForwardToSyslog = no
# 
ForwardToKMsg = no
# kernel log buffer (kmsg)
ForwardToConsole = no
# system console
TTYPath = /dev/console
ForwardToWall = yes
# sent as wall messages to all logged-in users
```

## remote

```sh
systemctl enable systemd-journal-gatewayd.service
systemctl start systemd-journal-gatewayd
#  Ab jetzt werden die Meldungen über Port 19531 ausgegeben, sie können mit einem Webbrowser angeschaut werden. 
```

# JOURNALCTL

- https://www.freedesktop.org/software/systemd/man/latest/journalctl.html#

If called without parameters, it will show the full contents of the journal, starting with the oldest entry

## solutions

```sh
journalctl --user /usr/bin/node -g "mdieterich"
journalctl --user --since today /usr/bin/node -f
journalctl --user --since "-2 days" /usr/bin/node -g "uweb" -e

journalctl --since today -t payload --priority 7 | fgrep "[total]"
```

## size & disk usage

```sh
sudo journalctl --disk-usage # shows the current disk usage of all journal files; shows the sum of the disk usage of all archived and active journal files.
du -sh /var/log/journal/ # alternative

journalctl --vacuum-size=100M # removes archived journal files until the disk space they use falls below the specified size 
# suffixes: K, M, G
journalctl --vacuum-files=5
journalctl --vacuum-time=2d # delete all entries older than two days
```

```sh
journalctl --rotate # This will mark the currently active journal logs as archive and create fresh new logs. 
# It’s optional but a good practice to do so.
```

## show

```sh
journalctl -e # Ende der Meldungen
journalctl -f 
journalctl --follow # shows only the most recent journal entries, and continuously print new entries as they are appended to the journal (tail -f /var/log/syslog)
journalctl -n <nr> # shows only the last nr. of lines; without nr. the default is 10 (= tail -n)
journalctl -x
journalctl --catalog # augment log lines with explanation texts from the message catalog. This will add explanatory help texts to log messages in the output where this is available.
```

## output format

```sh
journalctl -o json
journalctl -o json-pretty
journalctl -o export
journalctl -o verbose
journalctl -o verbose --output-fields=MESSAGE,PRIORITY
journalctl -o cat

journalctl --user-unit un-payloadcms.service --list-catalog
```

## boot

```sh
journalctl --list-boots # a tabular list; shows:
# boot nr. | boot ID | timestamp first mssg | timestamp last mssg
```

```sh
journalctl -b
journalctl --boot <nr> # shows messages from a specific boot (nr relative to the current boot)
journalctl --boot <id> # 

journalctl --boot 1 # the first boot found in the journal
journalctl --boot -0 # the last boot (defaultq)
journalctl --boot -1 # the boot before last
```

## time

```sh
journalctl -S yyyy-mm-dd hh:mm:ss
journalctl --since yyyy-mm-dd hh:mm:ss
journalctl -u nginx.service --since today
journalctl --user --since yesterday /usr/bin/node -g "uweb"
journalctl --since 09:00 --until "1 hour ago"
journalctl --user --since "2 days ago" /usr/bin/node -g "uweb" -e
journalctl --user --since "-2 days" /usr/bin/node -g "uweb" -e

journalctl -U yyyy-mm-dd hh:mm:ss
journalctl --until yyyy-mm-dd hh:mm:ss
```

shows entries on or newer/older than the specified date; format: 2012-10-30 18:17:16; if the date is omitted, the current day is assumed.

## grep

```sh
journalctl -g <pattern>
journalctl --grep <pattern> # filter output to entries where the MESSAGE= field matches the specified regular expression
# if the pattern is all lowercase, matching is case insensitive. 

journalctl --user-unit un-payloadcms.service -g rjuschka # 
journalctl --user /usr/bin/node -g "mdieterich"
journalctl --user --since "yesterday" /usr/bin/node -g "uweb"
```

## unit

```sh
journalctl -u
journalctl --unit <unit> # the same as
journalctl _SYSTEMD_UNIT <unit>

journalctl --user --unit # the same as
journalctl --user-unit <unit> # the same as
journalctl  _SYSTEMD_USER_UNIT=<unit>
```

## more filter

```sh
journalctl -k
journalctl --dmesg # Kernelmeldungen (Alternative zu dmesg)
```

### by priority

```sh
journalctl -p
journalctl --priority # filters output by message priorities or priority ranges
# takes a single numeric or textual log level or a range.
# emerg (0); alert (1); crit (2); err (3); warning (4); notice (5); info (6); debug (7)
journalctl -kp 0-4
```

### by identifier

```sh
journalctl -t=SYSLOG_IDENTIFIER
journalctl --identifier=SYSLOG_IDENTIFIER 
# Show messages for the specified syslog identifier SYSLOG_IDENTIFIER. 
# Can be specified multiple times.
```

### by path

```sh
journalctl --user /usr/bin/node -e # display all of the entries that involve the executable in question
```

## fields

```sh
journalctl -N
journalctl --fields # Print all field names currently used in all entries of the journal

journalctl -F 
journalctl --field # Print all possible data values the specified field can take in all entries of the journal.

journalctl --user-unit un-payloadcms.service --field PRIORITY # 

journalctl --user-unit un-payloadcms.service -o verbose --output-fields=MESSAGE,_COMM # has an effect only for the output modes which would normally show all fields (verbose, export, json, json-pretty, json-sse and json-seq), as well as on cat.

journalctl MESSAGE=
journalctl PRIORITY=6
journalctl _SYSTEMD_USER_UNIT=un-eleventy@-home-frida-code-web-projects-sites-rjuschka-11ty.service
journalctl _PID=8088
journalctl _COMM=node
journalctl _CMDLINE=node --inspect=127.0.0.1:2001 /home/frida/code/web/node_modules_global/bin/eleventy --config=./eleventy.cjs --watch --incremental
```

