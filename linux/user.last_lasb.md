# last / lastb

* The files `wtmp` and `btmp` might not be found. The system only logs information in these files if they are present. This is a local configuration issue. If you want the files to be used, they can be created with a simple `touch /var/log/wtmp`

# last

* searches back through the `/var/log/wtmp`

```sh
sudo last --dns --hostlast --since 2024-01-01
```

```sh
last # show a listing of last logged in users file and displays a list of all users logged in (and out) since that file was created

last --since <time> # Display the state of logins since the specified time. This is useful, e.g., to easily determine who was logged in at a particular time. The option is often combined with --until.
last --until <time>
last --present <time> # Display the users who were present at the specified time. This is like using the options --since and --until together with the same time.
```

```sh
# The pseudo user `reboot` logs in each time the system is rebooted
last reboot # will show a log of all the reboots since the log file was created.
```

# lastb

* `/var/log/btmp`

```sh
lastb # by default it shows a log of the /var/log/btmp file, which contains all the bad login attempts.
```

# time formats

```sh
# YYYY-MM-DD hh:mm:ss
last --since 2024-08-01 22:45:12
# YYYY-MM-DD hh:mm
last --since 2024-08-01 22:45 # seconds will be set to 00
# YYYY-MM-DD
last --since 2024-08-01 # time will be set to 00:00:00
# hh:mm:ss
last --since 22:45:12 # date will be set to today
# hh:mm
last --since 22:45 # (date will be set to today, seconds to 00)

last since now
last since yesterday # time is set to 00:00:00
last since today # time is set to 00:00:00
last since -5min
last since -5days
```