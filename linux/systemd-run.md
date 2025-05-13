
- [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd-run.html)
- https://0pointer.net/blog/dynamic-users-with-systemd.html
- [What Settings Are Currently Available For Transient Units?](https://systemd.io/TRANSIENT-SETTINGS/)
- launch a **interactive _transient_ service**
- run a service without having to write a unit file first
- ad-hoc executions while leveraging the benefits of systemd’s supervision and resource management features

`Press ^] three times within 1s to exit session` --> CTRL + 5

```bash
--unit=<name> # Use this unit name instead of an automatically generated one
--user # Talk to the service manager of the calling user
--host=frida@lem:22 # Execute the operation remotely
--machine=# Execute operation on a local container.
--working-directory=<dir>
--same-dir # use the current wd of the caller for the service to execute
--collect # Unload the transient unit after it completed, even if it failed. Normally, without this option, all units that ran and failed are kept in memory until the user explicitly resets their failure state with systemctl reset-failed or an equivalent command. On the other hand, units that ran successfully are unloaded immediately. If this option is turned on the "garbage collection" of units is more aggressive, and unloads units regardless if they exited successfully or failed. 
# This option is a shortcut for:
--property=CollectMode=inactive-or-failed

# Transient services
# ------------------
--pty # When invoking the command, the transient service connects its standard input, output and error to the terminal systemd-run is invoked on, via a pseudo TTY device. This allows running programs that expect interactive user input/output as services, such as interactive command shells.
--service-type=<type> # Sets the service type. Defaults to simple.
--service-type=oneshot # waits for the process
--setenv=
```


```bash
systemd-run --pty --property=DynamicUser=true --property=StateDirectory=wuff /bin/sh
```

# EXAMPLES

```sh
sudo systemd-run --unit=un.rsync --service-type=oneshot --pty un.rsync.sh
```
# TRANSIENT SERVICE

```bash

```

# TRANSIENT SCOPE

If a command is run as **transient scope unit**, it will be executed by **systemd-run** itself as parent process and will thus inherit the execution environment of the caller. However, the processes of the command are managed by the service manager similarly to normal services, and will show up in the output of **systemctl list-units**. Execution in this case is synchronous, and will return only when the command finishes. This mode is enabled via the `--scope` switch

