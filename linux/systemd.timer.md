# SYSTEMD.TIMER

* Es gibt immer eine zur timer unit passende gleichnamige service unit.
* The service unit does not require an `[Install]` section as it is the timer units that are enabled. 
* If necessary, it is possible to control a differently-named unit using the `Unit=` option in the timer's `[Timer]` section. 

# HOW TO

```sh
sudo nano /usr/local/bin/my-script.sh # Create script for the service unit to run
sudo nano /etc/systemd/system/<unit>.service # Create a service unit file
sudo nano /etc/systemd/system/<unit>.timer # Create a timer unit file
sudo systemd-analyze verify /etc/systemd/system/<unit> # Verify syntax
sudo systemctl start <unit>.timer # Start unit
sudo systemctl enable <unit>.timer # Enable timer unit
sudo journalctl -u <unit>.servie # Check Log 
sudo systemctl list-timers
```

## example

Script to be executed:

```bash
#!/bin/bash
rsync -aP /home/ /mnt/usb/
```

Service unit:

```shell
[Unit]
Description="A script to backup all home dirs to usb drive"

[Service]
ExecStart=/usr/local/bin/my-rsync.sh
```

Timer unit:

```bash
[Unit]
Description="Backs up the home directory"

[Timer]
OnBootSec=3min
OnUnitActiveSec=24h
Unit=my-rsync.service

[Install]
WantedBy=multi-user.target
```

# DIRECTIVES

- https://www.freedesktop.org/software/systemd/man/latest/systemd.timer.html#Options

```sh
RandomizedDelaySec=0
# Delay the timer by a randomly selected, evenly distributed amount of time between 0 and the specified time value. Defaults to 0, indicating that no randomized delay shall be applied. Each timer unit will determine this delay randomly before each iteration, and the delay will simply be added on top of the next determined elapsing time.
```
## monotonic

* **relative to an event**
* activate after a time span relative to a varying starting point
* verpasste Aufgaben werden nicht nachgeholt (they stop if the computer is temporarily suspended or shut down) 

```sh
OnBootSec=
# used to specify the amount of time after the system is booted when the associated unit should be activated.
OnActiveSec= 
# Defines a timer relative to the moment the timer unit itself is activated.
OnUnitActiveSec= 
# Defines the time relative to the last time the unit was activated.
OnUnitActiveSec=15min 
# a timer that runs every 15 minutes – for example at 1:02, 1:17, 1:32, 1:47, 2:02, … – always depending on the time it was last run.
```

## realtime

* activate on a calendar event, the same way that cronjobs do
* use `OnCalendar=` to define them
* If specified more than once, the timer unit will trigger whenever any of the specified expressions elapse.
* calendar timers and monotonic timers may be combined within the same timer unit.

```sh
OnCalendar= # If set to an empty string, the list of timers is reset (both OnCalendar= timers and monotonic timers), and all prior assignments will have no effect.
```

```sh
OnCalendar=DOW YYYY-MM-DD HH:MM:SS
# Trennzeichen!  
OnCalendar=Wed,Sat *-*-* 12:00:00
# jeden Samstag und Mittwoch um 12:00
OnCalendar=* *-*-1..7 19:00:00
# zwischen dem 1. und dem 7. jeden Monats um 19:00
OnCalendar=daily
OnCalendar=*:0/15
# every 15 minutes; translates to `*-*-* *:00/15:00`
OnCalendar=*:*:0/15
# every 15 seconds; translates to `*-*-* *:*:00/15`
```

```sh
Persistent=true
# bewirkt, das versäumte Jobs beim nächsten Rechnerstart sofort nachgeholt werden; the time when the service unit was last triggered is stored on disk. When the timer is activated, the service unit is triggered immediately if it would have been triggered at least once during the time when the timer was inactive. This is useful to catch up on missed runs of the service when the system was powered down.
```