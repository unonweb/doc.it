# LINKS

- https://www.freedesktop.org/software/systemd/man/latest/systemd.unit.html

# MODIFIERS

```sh
StandardOutput=file:%h/log_file
# %h the user’s home directory
```

# SPECIFIERS

* https://www.freedesktop.org/software/systemd/man/systemd.unit.html#Specifiers
* ideal for passing a path to a config file

```sh
# myunit@.service
[Service]
Description=OpenVPN Robust And Highly Flexible Tunneling Application On %I
# %I passes the argument verbatim (unescaped)
# for better readability
ExecStart = path/to/my/script.sh %i
# %i passes the argument, specially formatted (escaped)
```

| specifier | meaning                 | details                                                                                                                                  |
| --------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `%i`      | Instance name           | For instantiated units this is the string between the first "`@`" character and the type suffix. <br />Empty for non-instantiated units. |
| `%I`      | Unescaped instance name | Same as "`%i`", but with escaping undone                                                                                                 |

# USER UNITS

User Units unterscheiden sich von System Units nur in so fern, dass die Units nicht beim Systemstart mit gestartet werden, sondern erst wenn der Nutzer, für den die Unit aktiviert ist, sich auf dem System anmeldet. Dabei spielt es keine Rolle, ob die Anmeldung lokal direkt am Rechner erfolgt oder von außerhalb, z.B. über SSH. Die Unit wird automatisch wieder beendet, wenn der Nutzer vom System vollständig abgemeldet ist.

### start a systemd user instance for a user from a shell

`su` is a tool for changing user identities and very few other process credentials temporarily. It's not a tool for opening a completely new login session.

You'll need to login as your user via 

* the TTY
* your display manager
* `machinectl login``
* ``ssh`

to get a session that is clean enough to run the systemd user bus.

```sh
# --- 1 ---
/etc/systemd/user # place a unit file here
# systemd regards these unit files as user units
# they may be owned by root
# moreover, these units are available for all users
# the unit file must not have `user` or `group` attributes

# --- 2 ---
systemctl --user daemon-reload # let’s install the service
systemctl --user start <my-unit> # let’s start it

# --- 3 ---
systemctl --user enable <my-unit> # now the service starts automatically after our login. 
# Then, it’ll be running as long as we have some open sessions. 
# In other words, the service instance is bound to the user, not to the session.

# --- 4 ---
loginctl enable-linger <user> # if enabled for a specific user, a user manager is spawned for the user at boot and kept around after logouts. This allows users who are not logged in to run long-running services.

# --- 5 ---
journalctl --user -u <my-unit> # check the service’s log
```

# TEMPLATE UNITS

* a template unit file can be used for starting the same service with different parameters
* template unit filename: `myTemplate@.service`
* `myTemplate@<parameter>.service`

# AUTOMATIC DEPENDENCIES

- https://www.freedesktop.org/software/systemd/man/latest/systemd.unit.html#Automatic%20dependencies


# DIRECTIVES

* common configuration options of all the unit types
* metadata and configuring the relationship of the unit to other units
* types: **all**
* sections: **[Unit]** or **[Install]**

```ini
[Unit]
Description=

OnSuccess=
OnFailure=
```

## order

```ini
[Unit]
Before=
After= 
# configures ordering dependencies between units; space-separated list of unit names; this setting is independent of and orthogonal to the requirement dependencies as configured by Requires=, Wants= or BindsTo=; 
# After= ensures that the configured unit is started after the listed unit finished starting up, Before= ensures the opposite; if two units have no ordering dependencies between them, they are shut down or started up simultaneously
```

## dependencies & conditions

- conditions and asserts
  https://www.freedesktop.org/software/systemd/man/latest/systemd.unit.html#Conditions%20and%20Asserts

```ini
[Unit]
Requires= 
# hard depedencies; if this unit gets activated, the units listed here will be activated as well (simultaneously with the current unit by default); if one of the other units fails to activate, and an ordering dependency After= on the failing unit is set, this unit will not be started; this unit will be stopped if one of the other units is explicitly stopped; 
Wants= 
# soft dependencies; Systemd will attempt to start any units listed here (simultaneously by default) when this unit is activated; if these units are not found or fail to start, the current unit will continue to function; this is the recommended way to configure most dependency relationships.
Conflicts= 
# can be used to list units that cannot be run at the same time as the current unit; starting a unit with this relationship will cause the other units to be stopped
Condition...= 
# there are a number of directives that start with Condition which allow the administrator to test certain conditions prior to starting the unit; can be used to provide a generic unit file that will only be run when on appropriate systems; if the condition is not met, the unit is gracefully skipped.
```

### DefaultDependencies

- https://www.freedesktop.org/software/systemd/man/latest/systemd.unit.html#DefaultDependencies=

```sh
DefaultDependencies=true # default; a few default dependencies will implicitly be created for the unit. The actual dependencies created depend on the unit type.
```
 
 For example, for service units, these dependencies ensure that the service is started only after basic system initialization is completed and is properly terminated on system shutdown. See the respective man pages for details. 
 Generally, only services involved with **early boot or late shutdown** should set this option to `no`. It is highly recommended to leave this option enabled for the majority of common units. If set to `no`, this option does not disable all implicit dependencies, just non-essential ones.
## restart

* useful for configuring when Systemd should stop trying to restart a service

```ini
[Unit]
StartLimitIntervalSec=1s
# specifies a time interval in seconds
StartLimitBurst=5
# specifies the maximum amount of times that is allowed for the service to be restarted within the specified interval
```

## dep

used to define the behavior or a unit if it is enabled or disabled; enabling a unit marks it to be automatically started at boot; in essence, this is accomplished by latching the unit in question onto another unit that is somewhere in the line of units to be started at boot; this section is not interpreted by systemd during runtime

```ini
[Install]
WantedBy=
WantedBy=default.target
WantedBy=basic.target
WantedBy=graphical.target
RequiredBy= 
```

* creates a symbolic link in the `.wants/` or `.requires/` directory of each of the listed units when this unit is installed by `systemctl enable`
* this has the effect that a dependency of type `Wants=` or `Requires=` is added from the listed unit to the current unit
* the current unit will be started when the listed unit is started
* May be used more than once, or a space-separated list of unit names may be given.

## start on boot

```ini
# /etc/systemd/system/my-unit.service
[Unit]
Description=my first unit

[Service]
WorkingDirectory=/path/to/your/app
ExecStart=/usr/local/bin/node server.js
Type=simple
Restart=always
RestartSec=10

[Install]
WantedBy=basic.target
```

```sh
sudo systemctl enable node-server.service # creates a symlink into /etc/systemd/systemd/basic.target.wants
```

* `basic.target` is the first target hit after the core components of the system come online
* everything symlinked in `/etc/systemd/systemd/basic.target.wants` will be run as part of the `basic.target` step in the boot process