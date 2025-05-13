# PATHS

… nach Priorität:

## system

```sh
/etc/systemd/system/ # holds unit files that are user-provided
systemctl edit --full <unit> # legt eine Kopie der Originalunit in diesem Verzeichnis an

/run/systemd/system/ # the runtime location for unit files. Arbeitsverzeichnis für systemd
/lib/systemd/system # or
/usr/lib/systemd/user # the default location where unit files are installed by packages; unit files in the default directory should not be altered
```
## user

```sh
ll /usr/lib/systemd/user # where units provided by installed packages belong
ll /etc/systemd/user # where system-wide user units are placed by the system administrator.
```

```sh
~/.local/share/systemd/user # where units of packages that have been installed in the home directory belong.
~/.config/systemd/user/ # where the user puts their own units.
```

# CONFIG

- The same:
  https://www.freedesktop.org/software/systemd/man/latest/systemd-user.conf.html
  https://www.freedesktop.org/software/systemd/man/latest/systemd-system.conf.html

```sh
# system
sudo nano /etc/systemd/system.conf
ll /etc/systemd/system.conf.d/*.conf

# user global
sudo nano /etc/systemd/user.conf
ll  /etc/systemd/user.conf.d/*.conf

# user home
sudo nano ~/.config/systemd/user.conf
```

## recommendations

- https://github.com/konstruktoid/hardening/blob/master/systemd.adoc#etcsystemduserconf

```sh
[Manager]
DefaultLimitCORE=0 # Don’t allow core dumps.
DefaultLimitNOFILE=100 # Default limit for number of open files.
DefaultLimitNPROC=100 # Default limit for number of processes.
CapabilityBoundingSet=~CAP_SYS_PTRACE # remove this capability
```