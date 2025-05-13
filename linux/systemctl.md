# systemctl

```sh
systemctl # implies "list-units"
sudo systemctl reset-failed
```

# systemd manager

```sh
systemctl daemon-reload # reloads the systemd manager configuration; will rerun all generators, reload all unit files, and recreate the entire dependency tree.
```

```sh
systemctl -l
systemctl --full # Do not ellipsize unit names, process tree entries, journal output, or truncate unit descriptions in the output of status, list-units, list-jobs, and list-timers
```

```sh
systemctl --user # Talk to the service manager of the calling user, rather than the service manager of the system.
systemctl --system # Talk to the service manager of the system. This is the implied default
systemctl --global enable user_service.service # enable or disable the service for all users
systemctl --all # shows all units in memory
```

## filter

```sh
# by state
systemctl --failed # lists units in failed state; equivalent to --state=failed 
systemctl --state=help
```

```sh
# by type
systemctl --type mount
systemctl --type service
systemctl --type target # listet alle geladenen, aktiven mount/service/target units
systemctl --type mount --all # listet alle geladenen, auch inaktiv mount units
systemctl --user --type service,path
```

## import-environment

### import-environment into all user units

- https://unix.stackexchange.com/a/653101

A modern way (in 2021) is not to hard code the environment within the `.service` unit file. Instead, do something like this:

```bash
# cat /etc/X11/xinit/xinitrc.d/50-systemd-user.sh
#!/bin/sh

systemctl --user import-environment DISPLAY XAUTHORITY

if command -v dbus-update-activation-environment >/dev/null 2>&1; then
    dbus-update-activation-environment DISPLAY XAUTHORITY
fi
```

This is what is done in Arch Linux, for example.

Explanation: When starting X11, both the `DISPLAY` and the `XAUTHORITY` environment variables are inherited for _all_ systemd user service unit files (i.e. those managed by `systemd --user`).

One could check those are properly set by running `systemctl --user show-environment`.

By doing so, you don't need to use `Environment=` directives in your `.service` files.

# unit comands

## glob patterns

```sh
systemctl --user status 'un-eleventy@*.service' # need to quote a glob pattern!
```

## status

```sh
systemctl status # shows a general system overview

systemctl status <unit|filesystem> # unit or file system status followed by most recent log data from the journal; running status will load a unit; the command is thus not useful for determining if something was already loaded or not;
systemctl --user status un-* # shows all units starting with 'un-'
systemctl --user status --output=short # when used with status, controls the formatting of the journal entries that are shown.
```

## show

```sh
systemctl show <unit> # lists properties of *unit*; default = properties of the manager.
```

## is-active

```sh
systemctl --user is-active <unit> # 'failed', 'active'
systemctl --user is-active --quiet <unit> 
# will exit with status zero if service is active, non-zero otherwise, making it ideal for scripts
# echo $? --> exit status

systemctl --user is-active --quiet un-eleventy@-home-frida-code-web-projects-sites-11ty-rjuschka.service
eleventyDir='/home/frida/code/web/projects/sites/11ty/haerer-geruestbau'
systemctl --user is-active $(systemd-escape --template un-eleventy@.service ${eleventyDir})
```

## start | stop

```sh
systemctl start <unit>
systemctl stop <unit>
systemctl status <unit>

systemctl stop sshd@*.service # will stop all sshd@.service instances. Note that alias names of units, and units that aren't in memory are not considered for glob expansion. 

systemctl reload <unit> # reloads the service-specific configuration, not the unit configuration file of systemd. 
# If you want systemd to reload the configuration file of a unit, use the 'daemon-reload' command. 
# In other words: for the example case of Apache, this will reload Apache's httpd.conf in the web server, not the apache.service systemd unit file.

systemctl restart <unit> # Stop and then start one or more units specified on the command line. If the units are not running yet, they will be started. Note that restarting a unit with this command does not necessarily flush out all of the unit's resources before it is started again. For example, the per-service file descriptor storage facility (see FileDescriptorStoreMax= in systemd.service(5)) will remain intact as long as the unit has a job pending, and is only cleared when the unit is fully stopped and no jobs are pending anymore. If it is intended that the file descriptor store is flushed out, too, during a restart operation an explicit systemctl stop command followed by systemctl start should be issued.
```

## kill unit

```sh
systemctl kill <unit> # Send a signal to one or more processes of the unit. 
systemctl kill <unit> -s
systemctl kill <unit> --signal=
systemctl kill <unit> -s SIGTERM # default
systemctl kill <unit> -s SIGINT
systemctl kill <unit> -s SIGSTOP

systemctl kill -s help # lists the known values 
systemctl kill -s list # lists known values along with the numerical signal numbers

systemctl kill <unit> --kill-whom= # When used with kill, choose which processes to send a signal to.
systemctl kill <unit> --kill-whom main # kill only the main process (the one that defines the life-time of it)
systemctl kill <unit> --kill-whom control # kill only he control process (a control process of a unit is one that is invoked by the manager to induce state changes of it)
systemctl kill <unit> --kill-whom all # kill all processes of the unit. 
# For example, all processes started due to the ExecStartPre=, ExecStop= or ExecReload= settings of service units are control processes. Note that there is only one control process per unit at a time, as only one state change is executed at a time. For services of type Type=forking, the initial process started by the manager for ExecStart= is a control process, while the process ultimately forked off by that one is then considered the main process of the unit (if it can be determined). This is different for service units of other types, where the process forked off by the manager for ExecStart= is always the main process itself. A service unit consists of zero or one main process, zero or one control process plus any number of additional processes. Not all unit types manage processes of these types however. For example, for mount units, control processes are defined (which are the invocations of /usr/bin/mount and /usr/bin/umount), but no main process is defined. If omitted, defaults to all.
```

## list-timers

```sh
systemctl list-timers # listet konfigurierte timer units
systemctl list-timers --all # lists all timers (including inactive)
# NEXT shows the next time the timer will run.
# LEFT shows how long till the next time the timer runs.
# LAST shows the last time the timer ran.
# PASSED shows has long as passed since the timer laset ran.
# UNIT shows the name of the timer
# ACTIVATES shows the name the service the timer activates when it runs.
```

## list-units

```sh
systemctl # implies "list-units"
systemctl list-units
# lists units that systemd currently has in memory
# default = active, pending jobs, failed
# *load* reflects whether the unit definition was (properly) loaded into memory
# *active* summary state about whether the state of the unit (has it started successfully or not)
# *sub* more detailed information about the state of the unit; values depend on unit type.
systemctl --all # shows all units in memory

systemctl --user list-units un-*
```

## list-dependencies

```sh
systemctl --user list-dependencies <target> # list all units required by <target>
# By default, only target units are recursively expanded. 
systemctl --user list-dependencies <target> --all # all other units are recursively expanded as well
systemctl list-dependencies <unit> # shows units required and wanted by the specified <unit>
systemctl --user list-dependencies un-payloadcms.service --reverse
```

## list-machines

```sh
systemctl list-machines # list the host and all running local containers with their state
	# running # system is fully operational
	# degraded # system is operational, but one ore more units failed
```

## list-sockets

```sh
systemctl list-sockets # lists socket units currently in memory
```

## targets

```sh
systemctl list-units --type target --all # list all loaded units regardless of their state
systemctl list-units --type target # list all currently loaded target units
```

* **multi-user.target:**

  often used as the default target a system starts in

  starts everything that is needed for full system functionality and is commonly used on servers

* **graphical.target:**

  also is commonly used

  starts all units that are needed for full functionality, as well as a graphical interface

# unit file commands

## cat

```sh
systemctl cat <unit> # Shows the content of the unit file. Note that this shows the contents of the backing files on disk, which may not match the system manager's understanding of these units if any unit files were updated on disk and the daemon-reload command wasn't issued since

systemctl --user cat un-eleventy@.service
```

## edit

* after the units have been edited, systemd configuration is reloaded

```sh
systemctl edit <unit> # einzelne Zeilen an eine bestehende Unit-Datei anhängen; creates a diretory /etc/systemd/system/unit-to-edit.d/ which contains a file override.conf which will be merged with the full unit file when the unit is loaded

systemctl edit --full <unit> # die ganze Unit-Datei bearbeiten; this will copy the original unit file to /etc/systemd/system/
```

```sh
sudo systemctl edit NetworkManager.service 
# creates:
/etc/systemd/system/NetworkManager.service.d/override.conf
```
## enable / disable

```sh
systemctl enable <unit> 
systemctl disable <unit> 
# creates/removes the symlinks in all target directories (/etc/systemd/system/target) according to the [Install] section of the unit file; does not start/stop the unit

systemctl enable|disable <unit> --now 
# unit will also be started/stopped
systemctl disable --now 
# +2xTAB zeigt alle (nativen) laufenden Service-Units, die abgeschaltet werden können

systemctl reenable <unit> # Reenable one or more units, as specified on the command line. This is a combination of disable and enable and is useful to reset the symlinks a unit file is enabled with to the defaults configured in its [Install] section. This command expects a unit name only, it does not accept paths to unit files.
```

## get-default

```sh
systemctl get-default # get the default target
ls -l /usr/lib/systemd/system/default.target # the same

set-default <target> # set the default target to boot into; sets (symlinks) the default.target alias to the given target unit
```

## mask

```sh
systemctl mask|unmask <unit> 
# marks the unit as completely unstartable by linking it to /dev/null; preventes it from being started, automatically or manually, for as long as it is masked.
```

## list-unit-files

```sh
systemctl list-unit-files # lists all available unit files within the systemd paths, including those that systemd has not attempted to load
# 'static' unit file has no "install" section, which is used to enable a unit (hence it cannot be enabled); usually, this means that the unit performs a one-off action or is used only as a dependency of another unit and should not be run by itself.
systemctl list-unit-files --type target # lists available targets
systemctl list-unit-files *time* # listet alle unit-files, die time im Namen tragen
```

