- https://www.freedesktop.org/software/systemd/man/latest/systemd.service.html
- https://askubuntu.com/questions/1371102/running-systemd-service-as-user-rather-than-root

may include the following sections:

```ini
[Unit]

[Install]
```

```sh
man systemd.service # shows possible options for service units
```

# EXEC
## ExecStart

* Unless `Type=forking` is set, the process started via this command line will be considered the main process of the daemon.
* If `Type=oneshot` you can specify multiple `ExecStart`, they run serially not in parallel. 

```ini
ExecStart = path 
# commands that are executed when this service is started; only run after all ExecStartPre= commands (that were not prefixed with a "-") exit successfully

"@"	If the executable path is prefixed with "@", the second specified token will be passed as "argv[0]" to the executed process (instead of the actual filename), followed by the further arguments specified.

ExecStart = echo one ; echo "two two"
# This will execute echo two times
# Because two commands are specified, Type=oneshot must be used.

ExecStop = 
# commands to execute to stop the service started via ExecStart=

ExecStopPost = echo "service stopped"
# additional commands that are executed after the service is stopped.

ExecReload= 
# optional directive; indicates the command necessary to reload the configuration of the service if available
```

```sh
# You can explicitly invoke a shell to get shell parsing:
ExecStart=/bin/bash -c '/usr/local/bin/gunicorn --workers "$(nproc --all)" --threads "$(nproc --all)" --backlog 100 --bind 10.0.0.20:5000 -m 777 abc:app'
```

## ExecStartPre | ExecStartPost

```ini
ExecStartPre =
ExecStartPost = 
```

Additional commands that are executed before or after the command in `ExecStart=`. Syntax is the same as for `ExecStart=`, except that multiple command lines are allowed and the commands are executed one after the other, serially.

If any of those commands (not prefixed with "`-`") fail, the rest are not executed and the unit is considered failed.

`ExecStart=` commands are only run after all `ExecStartPre=` commands that were not prefixed with a "`-`" exit successfully.
# TYPE

```ini
[Service]
# process and daemonizing behavior
Type=simple|exec|forking|oneshot|dbus|notify|idle
```

It is generally recommended to use `Type=simple` for long-running services whenever possible, as it is the simplest and fastest option.

* **simple** # the service manager will consider the unit started immediately after the main service process (`ExecStart=`) has been forked off; the service manager will immediately proceed starting follow-up units, right after creating the main service process, and before executing the service's binary; this is the default if the Type= and Busname= are not set, but the ExecStart= is set; any communication should be handled outside of the unit through a second unit of the appropriate type (.socket unit) 
* **exec** # the service manager will consider the unit started immediately after the main service binary has been executed
* **forking** # is used when the service forks a child process, exiting the parent process almost immediately; this tells systemd that the process is still running even though the parent exited.
* **oneshot** # the service manager will consider the unit up after the main process exits; it will then start follow-up units (systemd waits for the process to exit before continuing on with other units); if this option is used without RemainAfterExit= the service will never enter "active" unit state, but directly transition from "activating" to "deactivating" or "dead" since no process is configured that shall run continously.

# RESTART

```ini
[Service]

Restart=always
Restart=on-success
Restart=on-abnormal
Restart=on-abort
Restart=on-watchdog
# whether the service shall be restarted when the service process exits, is killed, or a timeout is reached
Restart=on-failure
# the service will be restarted when the process exits with a non-zero exit code, is terminated by a signal (including on core dump, but excluding the aforementioned four signals), when an operation (such as service reload) times out, and when the configured watchdog timeout is triggered.

RestartSec=100ms
# configures the time to sleep before restarting a service

TimeoutSec=123
# amount of time (in seconds) that systemd will wait when starting or stopping the service before marking it as failed or forcefully killing it; you can set separate timeouts with TimeoutStartSec= and TimeoutStopSec= as well.
RemainAfterExit=true|false 
# commonly used with the oneshot type; indicates that the service should be considered active even after the process exits; defaults to no.
```

# EXAMPLES

## oneshot

```ini
# specify RemainAfterExit=true so that systemd considers the service as active after the setup action is successfully finished
# /etc/systemd/system/foo.service

[Unit]
Description=Setup foo
#After=network.target

[Service]
Type=oneshot
ExecStart=/opt/foo/setup-foo.sh
RemainAfterExit=true
ExecStop=/opt/foo/teardown-foo.sh
StandardOutput=journal

[Install]
WantedBy=multi-user.target
```

## notification unit

a simple bash script, that, when run, sends a notification to a channel of your choice (Slack, XMPP, Email, etc.) 

```ini
# /etc/systemd/system/failure-notification@.service

[Unit]
Description=Send a notification
After=network.target

[Service]
Type=simple
ExecStart=/path/to/failure-notification.sh %i
```

And then for any unit where you’d like to be informed of the failure, simply update or override that to do the notification:

```ini
[Unit]
OnFailure=failure-notification@%n
```

Here it will be templated out with `%n` meaning the unit name, which will replace the `%i` in the notification unit, and be included in the notification to you.