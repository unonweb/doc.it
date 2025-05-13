# LINKS

- freedesktop:
  https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#Sandboxing
- ctrl.blog:
  https://www.ctrl.blog/entry/systemd-service-hardening.html
  https://www.ctrl.blog/entry/systemd-opensmtpd-hardening.html
- fedora:
  https://fedoraproject.org/wiki/Changes/SystemdSecurityHardening
- linux-audit:
  https://linux-audit.com/systemd/how-to-harden-a-systemd-service-unit/
* docs.arbitrary.ch
  https://docs.arbitrary.ch/security/systemd.html
* mgdm.net
  https://mgdm.net/weblog/systemd/
* bertptrs.nl
  https://bertptrs.nl/2021/09/05/securely-passing-secrets-to-dynamicuser-systemd-services.html
* flashsystems.de
  https://flashsystems.de/articles/systemd-hardening/
* ruderich.org:
  https://ruderich.org/simon/notes/systemd-service-hardening
* Systemd Hardening Helper:
  https://github.com/synacktiv/shh
* Containers with systemd - Lennart Poettering
  https://www.youtube.com/watch?v=d4SwL2t5Yh4
* Lennart Poettering: "Containers without a Container Manager, with systemd"
  https://www.youtube.com/watch?v=sqhojVPr7xM&t=148s

Also note that some sandboxing functionality is generally not available in user services. Specifically, the various settings requiring file system namespacing support (such as `ProtectSystem=`) are not available, as the underlying kernel functionality is only accessible to privileged processes. However, most namespacing settings, that will not work on their own in user services, will work when used in conjunction with `PrivateUsers=true`

```sh
systemd-analyze security # get a security audit of all your `systemd` service
systemd-analyze security <service>
```

# OVERVIEW

## all

```sh
# USER
User=<user>
Group=<group>
PrivateUsers=false
DynamicUser=false
# implies:
#ProtectSystem=strict
#ProtectHome=read-only
#PrivateTmp=true
#RemoveIPC=true

# USERS
PrivateUsers=true # high impact, high risk

# FS SYSTEM
ProtectHome=read-only # read-only: /home, /root, /run/user
ProtectHome=true # inaccessible: /home, /root, /run/user
ProtectHome=tmpfs
ProtectSystem=off
ProtectSystem=true # read-only: /boot, /usr
ProtectSystem=full # read-only: /boot, /usr, /etc
ProtectSystem=strict # read-only: everything except /dev, /proc, /sys
ProtectControlGroups=true # /sys/fs/cgroup
PrivateDevices=true # /dev only pseudo devices
PrivateTmp=true # /tmp, /var/tmp # medium impact, low risk
PrivateDevices # medium impact, low risk
ProtectProc=invisible 
# /proc only own processes
# high impact, medium risk
ProtectProc=ptraceable # /proc only processes that can be ptrace()'ed
ProtectKernelTunables=true # /sys, /proc/sys, /proc/fs # low impact, low risk
ProtectKernelLogs=true # /proc/kmsg, /dev/kmsg # low impact, low risk
ProtectKernelModules=true # medium impact, low risk
ProtectControlGroups=true # medium impact, low risk

# RESOURCES
ProtectClock=true # low impact, medium risk
ProtectHostname=true # low impact, low risk
PrivateNetwork=true # high impact, very high risk
ProtectKernelModules=true
RestrictNamespaces=true
RestrictRealtime=true # low impact, low risk

# SYS CALLS
LockPersonality=true # medium impact, low risk
KeyringMode=private
SystemCallArchitectures=native # Disable non-native syscalls
# EXPLOIT PROTECTION
NoNewPrivileges=true # high impact, low risk # Incompatible with dynamically generated code at runtime, including JIT, executable stacks, C compiler code "trampoline"
RestrictSUIDSGID=true # medium impact, low risk # Best used with NoNewPrivileges
MemoryDenyWriteExecute=true # medium impact, medium risk
InaccessiblePaths=/dev/shm
SystemCallFilter=~memfd_create
```
## system service

### calls root process

* no `DynamicUser` possible
  because process must be owned by root 

```sh
# USER
User=root
Group=root

# FS SYSTEM
ProtectHome=
ProtectSystem=

ProtectControlGroups=true #
PrivateDevices=true #
ProtectProc=invisible # ptraceable
ProtectKernelTunables=true #
ProtectKernelLogs=true # 
PrivateTmp #

# RESOURCES
ProtectClock=true
ProtectHostname=true
PrivateNetwork=false # needs networking?!
ProtectKernelModules=true
RestrictNamespaces=true
RestrictRealtime=true

# SYS CALLS
LockPersonality=true
KeyringMode=private

# exploit protection
MemoryDenyWriteExecute=true
InaccessiblePaths=/dev/shm
SystemCallFilter=~memfd_create
```
### calls user process

```bash
User=<user> # optional
DynamicUser=true
LoadCredential=<creds-id>:<path>
ExecStart=/usr/bin/example.sh --config-file=${CREDENTIALS_DIRECTORY}/<creds-id>

# implied:
#ProtectSystem=strict
#ProtectHome=read-only
#PrivateTmp=true
#RemoveIPC=true
```

## user service

* no `DynamicUser` possible
  because process must be owned by user

## strategies

* /home/$USER/.local/bin: 
    * scripts are only available to $USER
    * they *may* require sudo privileges depending on the cmds in it

sudo is required when:
- it's a system service
- it's a user service but exec script is not world-readable
- it's a user service but a script cmd requires root privileges

credentials:
- **user unit**:
    - owned by a specific user
    - stored in `~/.credentials`
- **system unit**:
    - 
### interactive app
- `systemd-run`
- **user unit**:
    - automatically has wayland environment
    - interactive via gui
    - secrets:
        - file must be owned by user
        - each user needs a copy of it
- **system unit**:
    - requires wayland environment setup:
      `User=frida`
      `Environment=WAYLAND_DISPLAY=wayland-0`
      `Environment=XDG_RUNTIME_DIR=/run/user/1000`
    - has access to secrets owned by root
    
### service app
  - no interaction
  - dynamicUser or static system user
  - LoadCredentials

# MY DEFAULTS


```sh
PrivateUsers=true
ProtectHome=read-only
ProtectSystem=strict
ProtectKernelTunables=true
PrivateDevices=true
PrivateNetwork=true
PrivateTmp=true
NoNewPrivileges=true
RestrictSUIDSGID=true

ProtectClock=true
ProtectHome=true
ProtectHostname=true
ProtectKernelLogs=true
ProtectKernelModules=true

SystemCallArchitectures=native
SystemCallFilter=
SystemCallFilter=@system-service
SystemCallFilter=~@aio @chown @clock @cpu-emulation @debug @keyring @memlock @module @mount @obsolete @privileged @raw-io @reboot @resources @setuid @swap userfaultfd mincore

CapabilityBoundingSet=
KeyringMode=private
LockPersonality=true
MemoryDenyWriteExecute=true





# Permit AF_UNIX for syslog(3) to help debugging. (Empty setting permits all
# families! A possible workaround would be to blacklist AF_UNIX afterwards.)
RestrictAddressFamilies=
RestrictAddressFamilies=AF_UNIX
RestrictNamespaces=true
RestrictRealtime=true


```

# HOW TO

## figure out required paths

### strace
**trace system calls and signals**

```bash
strace -f
strace --follow-forks # also trace child processes

strace -o FILE
strace --output=FILE # send trace output to FILE instead of stderr
```

The easiest way is to run the software through [`strace`](https://strace.io/) and use it as normal. `strace` can track and print the file system operations a program performs. More importantly, it can print the file paths a program interacts with.

The following command launches OpenSMTPD and traces which files it interacts with under the `==/etc==` directory.

```shell
/usr/bin/strace -f \
  /usr/sbin/smtpd 2>&1 | \
  egrep -o '"/etc[^"]+"'
```

This should give you the information you need to create an allow-list. Note that you must use the program normally for a while as its behavior, and thus file operations, may change over time and when using different functionality.

## test sandbox

### dropping a shell inside a systemd service

While hardening a service, it often happens that you want a shell inside a hardened systemd unit, for example to check access to files, or check the network connectivity. One way to do this is to use tmux to create a session inside the service, and attaching to it outside of the service. 

```sh
ExecStart="${pkgs.tmux}/bin/tmux -S /tmp/tmux.socket new-session -s my-session -d"
ExecStop="${pkgs.tmux}/bin/tmux -S /tmp/tmux.socket kill-session -t my-session"
```
### systemd-run

```sh
systemd-run \
--pty \
--user \
--collect \
--service-type=oneshot \
--property=ProtectKernelLogs=true \
bash

--property=NoNewPrivileges=true \
--property=ProtectSystem=true \
--property=PrivateUsers=true \
--property=PrivateTmp=true \
--property=ProtectProc=invisible \
--property=PrivateDevices=true \
bash
```

# EXAMPLES

```sh
User=www-data
Group=www-data
# this config prevents nginx to set /run/nginx.pid
# so add
RuntimeDirectory=nginx # config nginx to write it pid to /run/nginx/nginx.pid
# then bind() to 0.0.0.0:80 failed: Permission denied
AmbientCapabilities=CAP_NET_BIND_SERVICE
```

## github.com/YvesCousteau

- https://github.com/YvesCousteau/nix-system-services-hardened/tree/main/services

### NetworkManager.service

```sh
NoNewPrivileges=true
ProtectHome=true
ProtectKernelModules=true
ProtectKernelLogs=true
ProtectControlGroups=true
ProtectClock=true
ProtectHostname=true
ProtectProc=invisible
PrivateTmp=true
RestrictRealtime=true
RestrictAddressFamilies=AF_UNIX AF_NETLINK AF_INET AF_INET6 AF_PACKET
RestrictNamespaces=true
RestrictSUIDSGID=true
MemoryDenyWriteExecute=true
SystemCallFilter=~@mount ~@module ~@swap ~@obsolete ~@cpu-emulation ptrace
SystemCallArchitectures=native
LockPersonality=true
```

### cups.service

```sh
NoNewPrivileges=true
ProtectSystem=full
ProtectHome=true
ProtectKernelModules=true
ProtectKernelTunables=true
ProtectKernelLogs=true
ProtectControlGroups=true
ProtectHostname=true
ProtectClock=true
ProtectProc=invisible
RestrictRealtime=true
RestrictNamespaces=true
RestrictSUIDSGID=true
RestrictAddressFamilies=AF_UNIX AF_NETLINK AF_INET AF_INET6 AF_PACKET
MemoryDenyWriteExecute=true
SystemCallFilter=~@clock ~@reboot ~@debug ~@module ~@swap ~@obsolete ~@cpu-emulation
SystemCallArchitectures=native
LockPersonality=true 
```

### sshd.service

```sh
NoNewPrivileges=true
ProtectSystem=strict
ProtectHome=read-only
ProtectClock=true 
ProtectHostname=true
ProtectKernelTunables=true
ProtectKernelModules=true
ProtectKernelLogs=true
ProtectControlGroups=true 
ProtectProc=invisible
PrivateTmp=true
PrivateMounts=true
PrivateDevices=true
RestrictNamespaces=true
RestrictRealtime=true
RestrictSUIDSGID=true
MemoryDenyWriteExecute=true
LockPersonality=true
DevicePolicy=closed
SystemCallFilter=~@keyring ~@swap ~@clock ~@module ~@obsolete ~@cpu-emulation
SystemCallArchitectures=native
```
## my examples
### signal

```sh
systemd-run \
--user \
--collect \
--unit=signal-desktop-run \
--service-type=oneshot \
--property=NoNewPrivileges=true \
--property=RestrictSUIDSGID=true \
--property=PrivateUsers=true \
--property=PrivateTmp=true \
--property=PrivateIPC=true \
--property=DeviceAllow=char-drm \
--property=DevicePolicy=closed \
--property=ProtectKernelTunables=true \
--property=ProtectKernelLogs=true \
--property=ProtectKernelModules=true \
--property=ProtectClock=true \
--property=ProtectHostname=true \
--property=RestrictRealtime=true \
--property=LockPersonality=true \
--property=SystemCallFilter= \
--property=KeyringMode=private \
--property=SystemCallArchitectures=native \
--property=ProtectSystem=strict \
--property=ProtectHome=read-only \
--property=ReadWritePaths=/home/$USER/downloads \
--property=ReadWritePaths=/home/$USER/.config/Signal \
--property=ReadWritePaths=/run/user/$UID/dconf/user \
signal-desktop

--property=StandardOutput=journal \
--property=StandardError=journal \
--property=ReadOnlyPaths=/home/frida/ \

# issue:
# if I use --property=SystemCallFilter=~@mount
# the app breaks. But if I use it not it propably circumvents the namespace protections

# breaks:
--property=SystemCallFilter=~@mount \
--property=MemoryDenyWriteExecute=true \
--property=RestrictNamespaces=true \ 
--property=PrivateDevices=true \

# only system: 
--property=ProtectProc=invisible \


```

### wds

```ini
# required:
/usr/lib/node_modules
/home/frida/node_modules
/tmp/node-compile-cache
/home/frida/.npm
/usr/bin/node
/usr/bin/npx
# readonly
/etc/ld.so.cache
/etc/resolv.conf
/etc/nsswitch.conf
/etc/netsvc.conf
/dev/null
/usr/lib/locale/locale-archive
```

# OTHER: ALL

## NoNewPrivileges

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/systemd.exec.html#NoNewPrivileges=)
* https://docs.kernel.org/userspace-api/no_new_privs.html
* The simplest and most effective way to **ensure that a process and its children can never elevate privileges again**
  (e.g. via setuid or setgid bits, or filesystem capabilities)
* only has an effect on the unit's processes themselves (or any processes directly or indirectly forked off them). 
* It has no effect on processes potentially invoked on request of them through tools such as `at`, `crontab`, `systemd-run`, or arbitrary IPC services.
* use together with: `RestrictSUIDSGID`

```
NoNewPrivileges
RestrictSUIDSGID
```

```sh
systemd-run \
--user \
--pty \
--collect \
--service-type=oneshot \
--property=NoNewPrivileges=true \
--property=RestrictSUIDSGID=true \
sudo -l
# sudo: The "no new privileges" flag is set, which prevents sudo from running as root
```
## ProtectSystem

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#ProtectSystem=)
* **mounts read-only:**
  * **true**: `/boot`, `/usr`
  * **full**: `/boot`, `/usr`, `etc` => high impact, medium risk
  * **strict**: everything except `/dev`, `/proc`, `/sys` => very high impact, very high risk
* combine with:
    * `PrivateDevices`, `ProtectKernelTunables`, `ProtectControlGroups`
    * `ReadWritePaths`: exclude specific directories from being made read-only
    * `StateDirectory`, `LogsDirectory`, …
* risk:
    * May break e.g webservers using ACME to renew their own keys which _may_ be in `/etc`

Turn this on for all services which are not involved with system updates or need to modify the operating system in other ways.

```sh
ProtectSystem=true # mounts /boot and /usr directories read-only
ProtectSystem=full # /etc/ directory is mounted read-only, too.
ProtectSystem=strict # the entire file system hierarchy is mounted read-only, except for the API file system subtrees /dev/, /proc/ and /sys/ (protect these directories using PrivateDevices=, ProtectKernelTunables=, ProtectControlGroups=)
```

```sh
# allow only access to one path:
ProtectSystem=strict
ReadWritePaths=/var/log/write-test
```

## RestrictRealtime

* risk: low
* May prevent denial-of-service situations
* https://linux-audit.com/kernel/scheduler/
* https://linux-audit.com/systemd/settings/units/restrictrealtime/

The Linux scheduler defines which process gets how much CPU resources, such as the time slice and at what priority. With the setting RestrictRealtime, systemd can block services from making use of several real-time policies.

Related syscalls to determine if a service needs access to real-time scheduling policies:

- sched_yield(2)
- sched_setaffinity(2)

To find out if access may be needed, search in the source code, run `strace`, or inspect the related binary. For example for nginx, the output looks like this:

```plaintext
# strings /usr/sbin/nginx | grep sched_
sched_setaffinity
sched_yield
sched_setaffinity(): using cpu #%ui
sched_setaffinity() failed
```

This section is under development and new settings are being added.

## IPAddressAllow, IPAddressDeny

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/systemd.resource-control.html#IPAddressAllow=ADDRESS%5B/PREFIXLENGTH%5D%E2%80%A6)
* **network traffic filtering**

Turn on address range network traffic filtering for IP packets sent and received over `AF_INET` and `AF_INET6` sockets. Both directives take a space separated list of IPv4 or IPv6 addresses, each optionally suffixed with an address prefix length in bits (separated by a "/" character). If the latter is omitted, the address is considered a host address, i.e. the prefix covers the whole address (32 for IPv4, 128 for IPv6).

# USER

## User / Group

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#User=)
* **Set the UNIX user/group that the processes are executed as**
* Takes a single user or group name, or a numeric ID as argument. 
* if no group is set, the default group of the user is used.
* This setting does not affect commands whose command line is prefixed with "`+`".

**root**
For **system services** and for user services of the root user, the default is `root`, but `User=` may be used to specify a different user. 

**user**
For **user services** of any other user, switching user identity is not permitted, hence the only valid setting is the same user the user's service manager is running as. 

**DynamicUser**
When used in conjunction with `DynamicUser=` the user/group specified is dynamically allocated at the time the service is started, and released at the time the service is stopped — unless it is already allocated statically (see below). 

If `DynamicUser=` is not used, the specified user/group must have been created statically in the user database, for example using the sysusers.d facility, which is applied at boot or package install time. If the user does not exist by then program invocation will fail.

## DynamicUser

- https://0pointer.net/blog/dynamic-users-with-systemd.html
- [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#DynamicUser=)
- a unit making use of dynamic user/group allocation cannot leave files around after unit termination
- Do not use for:
    - services that need to write to files outside of systemd standard paths (`/run/<package>`,  `/var/lib/<package>`, `/var/cache/<package>`, `/var/log/<package>`,  `/var/tmp`, `/tmp`, `/dev/shm`)
    - services which run as root (obviously…) or are otherwise privileged
    - services that need to live in the same mount name-space as the host system (for example, because they want to establish mount points visible system-wide)

```sh
DynamicUser=true

# implies:
ProtectSystem=strict
ProtectHome=read-only
PrivateTmp=true
RemoveIPC=true # ensures that when the service goes down all SysV and POSIX IPC objects (shared memory, message queues, semaphores) owned by the service's user are removed
NoNewPrivileges=true
RestrictSUIDSGID=true

```

A brand new user and group will be allocated when the service starts. Once the service exits, the user is removed – it never actually exists in `/etc/passwd`. This makes it harder to create a persistent route in to a system after an exploit.

Do not use `BindPaths=` and be careful with `AF_UNIX` file descriptor passing for directory file descriptors, as this would permit processes to create files or directories owned by the dynamic user/group that are not subject to the lifecycle and access guarantees of the service

### allow open ports below 1024

```sh
AmbientCapabilities=CAP_NET_BIND_SERVICE
DynamicUser=true
```

# OTHER: SYSTEM ONLY

## RootImage

- [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#RootImage=)

## ProtectControlGroups

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/systemd.exec.html#ProtectControlGroups=)

If true, the Linux Control Groups hierarchies accessible through _/sys/fs/cgroup_ will be made read-only to all processes of the unit. Except for container managers no services should require write access to the control groups hierarchies; it is hence recommended to **turn this on for most services**.

## ProtectProc

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#ProtectProc=)
* controls which directories with process metainformation (/proc/_PID_) are visible and accessible
* `ProtectProc=invisible` => high impact, medium risk
* The root user is unaffected by this option
* To be effective it has to be used together with `User=` or `DynamicUser=yes`, and also without the `CAP_SYS_PTRACE` capability, which also allows a process to bypass this feature.
```bash
ProtectProc=invisible # processes owned by other users are hidden from /proc/
ProtectProc=ptraceable # all processes that cannot be ptrace()'ed by a process are hidden to it. 
ProtectProc=default # no restrictions on /proc/ access or visibility are made
```

```sh
CapabilityBoundingSet=~CAP_SYS_PTRACE # remove
```

# MOUNT NS
## BindPaths, BindReadOnlyPaths

- [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#BindPaths=)

It is not possible to use those options for mount points nested underneath paths specified in `InaccessiblePaths=`, or under `/home/` and other protected directories if `ProtectHome=yes` is specified. `TemporaryFileSystem=` with "`:ro`" or `ProtectHome=tmpfs` should be used instead.

implies that a mount namespace is allocated for the unit, i.e. it implies the effect of `PrivateMounts=true`.


## Directories: runtime, state, cache, logs, config

* RuntimeDirectory
* StateDirectory
* CacheDirectory
* LogsDirectory
* ConfigurationDirectory

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#RuntimeDirectory=)
* the lifetime of the directories is tied directly to the lifetime of the unit
* The directories defined by these options are always created under the standard paths used by systemd (`/var/`, `/run/`, `/etc/`, …). If the service needs directories in a different location, a different mechanism has to be used to create them.

| Directory                 | Below path for system units | Below path for user units | Environment variable set   |
| ------------------------- | --------------------------- | ------------------------- | -------------------------- |
| `RuntimeDirectory=`       | `/run/`                     | `$XDG_RUNTIME_DIR`        | `$RUNTIME_DIRECTORY`       |
| `StateDirectory=`         | `/var/lib/`                 | `$XDG_STATE_HOME`         | `$STATE_DIRECTORY`         |
| `CacheDirectory=`         | `/var/cache/`               | `$XDG_CACHE_HOME`         | `$CACHE_DIRECTORY`         |
| `LogsDirectory=`          | `/var/log/`                 | `$XDG_STATE_HOME/log/`    | `$LOGS_DIRECTORY`          |
| `ConfigurationDirectory=` | `/etc/`                     | `$XDG_CONFIG_HOME`        | `$CONFIGURATION_DIRECTORY` |

### RuntimeDirectory

Use `RuntimeDirectory=` to manage one or more runtime directories for the unit and bind their lifetime to the daemon runtime. This is particularly useful for unprivileged daemons that cannot create runtime directories in `/run/` due to lack of privileges, and to make sure the runtime directory is cleaned up automatically after use. 

For runtime directories that require more complex or different configuration or lifetime guarantees, please consider using [tmpfiles.d(5)](https://www.freedesktop.org/software/systemd/man/latest/tmpfiles.d.html#).

```sh
RuntimeDirectory=foo/bar baz
```

* The service manager creates `/run/foo` (if it does not exist), `/run/foo/bar`, and `/run/baz`.
* The directories `/run/foo/bar` and `/run/baz` except `/run/foo` are owned by the user and group specified in `User=` and `Group=`
* Directories are removed when the service is stopped.

### LoadCredential

* https://bertptrs.nl/2021/09/05/securely-passing-secrets-to-dynamicuser-systemd-services.html
* [[systemd.exec]]


```sh
[Unit]
Description=Example service that depends on secrets

[Service]
Type=simple
DynamicUser=yes
LoadCredential=example-service.conf:/etc/example-service.conf
ExecStart=/usr/bin/example.sh --config-file=${CREDENTIALS_DIRECTORY}/example-service.conf
```

Now the user the service runs as no longer matters for its access to the secrets at all. The real config file can be readable for `root` only and it will still work fine.

# USER NS

- [_linux.container](obsidian://open?vault=doc&file=it%2Flinux%2F_linux.container)

These options require **unprivileged user namespaces support**.

These options are available for:
- system services
- user services with `PrivateUsers=true` 

## PrivateUsers

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#PrivateUsers=)
* https://linux-audit.com/systemd/settings/units/privateusers/
* **Define a new user namespace for the process and its children**
* detach the user and group databases used by the unit from the rest of the system
* All files, directories, processes and other resources owned by users/groups not equaling `root` or the unit's own will stay visible from within the unit but appear owned by `nobody`
* `PrivateUsers=true` and `PrivateUsers=self`
    * Map root user/group
    - Map user/group of unit (`User=`)
    - Map every other user to user `nobody`
- To have effective sandbox one does need user namespace, otherwise root in the container is root outside of the container. To sandbox it to the limits of one user, **root in the container should be at most that user outside of the container**.
- **all unit processes are run without privileges in the host user namespace** 
  (regardless if the unit's own user/group is `root` or not)
- The process will have **zero process capabilities on the host's user namespace**, but full capabilities within the service's user namespace. 

Settings such as `CapabilityBoundingSet=` will affect only the latter, and there's no way to acquire additional capabilities in the host's user namespace.

```bash
PrivateUsers=true
PrivateUsers=self 
# a minimal user and group mapping is configured that maps the "root" user/group as well as the unit's own user/group to themselves and everything else to "nobody". This is useful to securely detach the user and group databases used by the unit from the rest of the system, and thus to create an effective sandbox environment. All files, directories, processes, IPC objects and other resources owned by users/groups not equaling "root" or the unit's own will stay visible from within the unit but appear owned by the "`nobody`" user and group.
```

### user service

In a **per-user instance of the service manager**, the mapping of `root` user/group to itself is omitted (unless the user manager is root).

Additionally, in the per-user instance manager case, the user namespace will be set up before most other namespaces. This means that combining `PrivateUsers=true` with other namespaces will enable use of features not normally supported by the per-user instances of the service manager.

### validate

```sh
sudo systemd-run \
--pty \
--collect \
--service-type=oneshot \
--property=PrivateUsers=true \
bash

sudo lsns
# 4026532682 user        1 24025 root             ├─/usr/bin/bash
```

#### access other user's files

```sh
# even root access files in directories of other users
sudo systemd-run \
--pty \
--collect \
--service-type=oneshot \
--property=PrivateUsers=true \
touch /home/frida/test
# cannot touch '/home/frida/test': Permission denied

# but root can access its own files
sudo systemd-run \
--pty \
--collect \
--service-type=oneshot \
--property=PrivateUsers=true \
tail /root/.zshrc
```

### ProtectHostname

* Implementation risk: very low
* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#ProtectHostname=)
* https://linux-audit.com/systemd/settings/units/protecthostname/

A process rarely needs to change the hostname or NIS domain name of the system. In this case the ProtectHostname can be used to prevent this.

```systemd
[Service]
ProtectHostname=yes
```

To know if changes to the hostname or NIS domain name are needed, we can look for the following syscalls:

- sethostname(2)
- setdomainname(2)

If these are not present, then this setting can be enabled.

## PrivateTmp

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#PrivateTmp=)
* **secure access to temporary files of the process, but makes sharing between processes via _/tmp_ or _/var/tmp_ impossible**
* additionally all temp files created by a service in these directories will be removed after the service is stopped

sets up `/tmp` and `/var/tmp` for the service in a way that it gets its own, disconnected version of these directories, that are not shared by other services, and whose life-cycle is bound to the service's own life-cycle.

Background: These directories are world-writable. If another process does not set its permissions correctly then a rogue process may modify the files of the other one to manipulate it.


```
PrivateTmp=true
```

### validate

```sh
sudo systemd-run \
--pty \
--collect \
--service-type=oneshot \
--property=PrivateUsers=true \
--property=PrivateTmp=true \
ls -l /tmp # total 0
```

## ProtectKernelModules

* **medium impact, low risk**
* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#ProtectKernelModules=)
* https://linux-audit.com/systemd/settings/units/protectkernelmodules/

Most services do not need to load new kernel modules. With systemd unit setting ProtectKernelModules the explicit loading of kernel modules can be blocked.

This setting can be applied to most system services. Some software, especially focused on network traffic capture, may use a custom kernel module and have the need to load it. But otherwise it is safe to block it, preventing any unauthorized loading of kernel modules.

```systemd
[Service]
ProtectKernelModules=yes
```

## ProtectKernelTunables

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/systemd.exec.html#ProtectKernelTunables=)
* **make kernel variables read-only to all processes of the unit**
* `/sys`
* `/proc/sys`
* `/proc/fs`
- **low impact, low risk**
- Almost no services need to write to these at runtime
  => recommended to turn this on for most services.

If true, **kernel variables** accessible through _/proc/sys_, _/sys_, _/proc/sysrq-trigger_, _/proc/latency_stats_, _/proc/acpi_, _/proc/timer_stats_, _/proc/fs_ and _/proc/irq_ will be made read-only to all processes of the unit. 

Usually, tunable kernel variables should only be written at boot-time, with the sysctl.d mechanism. 

## PrivateIPC

- [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#PrivateIPC=)
- does not have an effect on `AF_UNIX` sockets, which are the most common form of IPC used on Linux. Instead, `AF_UNIX` sockets in the file system are subject to mount namespacing, and those in the abstract namespace are subject to network namespacing
- only has an effect on SysV IPC (which is mostly legacy) as well as POSIX message queues (for which `AF_UNIX`/`SOCK_SEQPACKET` sockets are typically a better replacement)

## PrivateMounts

- [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#PrivateMounts=)
- block: mount propagation unit > host
  the processes of this unit will be run in their own private file system (mount) namespace
- allow: propagation host > unit processes
  file system mount points established or removed on the host will be propagated to the unit's processes
- primarily useful to explicitly request file system namespacing if none of the other namespace settings are used:
  `PrivateTmp=`, `PrivateDevices=`, `ProtectSystem=`, `ProtectHome=`, `ReadOnlyPaths=`, ...

## ProtectHome

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#ProtectHome=)
* **deny access to private user data**
    * `/home/`
    * `/root`
    * `/run/user`
* `ProtectHome=true`:
  Mostly equivalent to `InaccessiblePaths=/home/ /root /run/user`
  Some applications may need persistent data stored in `XDG_CONFIG_HOME`. 
  Then `StateDirectory=` can be used to mitigate some of the negative consequences.
* **`ProtectHome=read-only`**: 
  mostly equivalent to `ReadOnlyPaths=`
  Nest `ReadWritePaths=` inside of `ReadOnlyPaths=` in order to provide writable subdirectories within read-only directories
  Ideal for backup services
* **`ProtectHome=tmpfs`**: 
  mostly equivalent to `TemporaryFileSystem=` with "`:ro`"

```sh
ProtectHome=true # inaccessible: /home/, /root, and /run/user
PrivateUsers=true # required for user services
```

```bash
ProtectHome=read-only # Ideal for backup services
ProtectHome=tmpfs # temporary file systems are mounted on the three directories in read-only mode. Useful to hide home directories not relevant to the processes invoked by the unit, while still allowing necessary directories to be made visible when listed in `BindPaths=` or `BindReadOnlyPaths=`.
```

### validate

```sh
systemd-run \
--pty \
--user \
--collect \
--service-type=oneshot \
--property=PrivateUsers=true \
--property=ProtectHome=read-only \
touch /home/frida/test 
# cannot touch '/home/test': Read-only file system

sudo systemd-run \
--pty \
--collect \
--service-type=oneshot \
--property=PrivateUsers=true \
--property=ProtectHome=read-only \
touch /home/frida/test
# cannot touch '/home/frida/test': Permission denied
# this is because of PrivateUsers=true
```

## PrivateDevices

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/systemd.exec.html#PrivateDevices=)
* **securely turn off physical device access by the executed process**
* When access to some but not all devices must be possible, the `DeviceAllow=` setting might be used instead

sets up a new /dev mount for the executed processes and only adds API pseudo devices such as _/dev/null_, _/dev/zero_ or _/dev/random_ (as well as the pseudo TTY subsystem) to it, but no physical devices such as _/dev/sda_, system memory _/dev/mem_, system ports _/dev/port_ and others.

### validate

```sh
sudo systemd-run \
--pty \
--collect \
--service-type=oneshot \
--property=PrivateUsers=true \
--property=PrivateDevices=true \
ls -l /dev
```

### DeviceAllow

- [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.resource-control.html#DeviceAllow=)
- Control access to specific device nodes by the executed processes
- When access to _all_ physical devices should be disallowed, `PrivateDevices=` may be used instead
- For most services it might be easier to use `PrivateDevices=true`or set `DevicePolicy` to reduce the devices that can be accessed.
- specify devices by group: you may hence use the "`*`" and "`?`" wildcards
- specify devices by path: wildcards are not available!

```sh
cat /proc/devices 
```

```sh
DeviceAllow=/dev/sda1 # specific device by path
DeviceAllow=/dev/sda3 r # 
DeviceAllow=block-* # all block devices
DeviceAllow=char-* # all char devices
DeviceAllow=char-rtc r # 
```

### DevicePolicy

- [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.resource-control.html#DevicePolicy=auto%7Cclosed%7Cstrict)
- Control the policy for allowing device access

```sh
DevicePolicy=strict # only allow types of access that are explicitly specified.
DevicePolicy=closed # in addition, allows access to standard pseudo devices including `/dev/null`, `/dev/zero`, `/dev/full`, `/dev/random`, and `/dev/urandom`.
DevicePolicy=auto # in addition, allows access to all devices if no explicit DeviceAllow= is present. This is the default.
```

## PrivateNetwork

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/systemd.exec.html#PrivateNetwork=)
* **turn off network access by the executed process**
* sets up a new network namespace for the executed processes
* configures only the loopback network device inside it
* No other network devices will be available to the executed process.

## TemporaryFileSystem

- [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#TemporaryFileSystem=)
- **Hide files or directories not relevant to the processes invoked by the unit**
- All required paths must be mounted into this jail via `BindReadOnlyPaths` and `BindPaths`

`TemporaryFileSystem` mounts tmpfs over the specified directories. 

This is similar to `InaccessiblePaths` which also prevents access to the directory contents but `TemporaryFileSystem` permits nesting to give access to sub-directories. 

This restrictive use of `TemporaryFileSystem` is especially important for privileged processes which still have access to all root-owned files even with all the other restrictions from above. As this often includes private keys restricting access via `TemporaryFileSystem` is very useful.

All path based restrictions (e.g. from previous paragraph or `TemporaryFileSystem`) can be undone by a privileged process with the ability to perform mount syscalls. The `CapabilityBoundingSet` and `SystemCallFilter` settings above prevent this but one should be aware of this potential issue.

### ex

```sh
TemporaryFileSystem=/var:ro
BindReadOnlyPaths=/var/lib/systemd
```

The invoked processes by the unit cannot see any files or directories under `/var/` except for `/var/lib/systemd` or its contents.

### ex

```sh
[Unit]
Description=Example unit

[Service]
ExecStart=/home/someuser/executable
User=someuser
Group=someuser
TemporaryFileSystem=/:ro
PrivateTmp=true
BindReadOnlyPaths=/usr/lib
BindReadOnlyPaths=/lib64
BindReadOnlyPaths=/lib
BindPaths=/home/someuser/executable
```

This is a minimal example and most application will need more paths whitelisted. Some common paths include:

- /etc/ca-certificates, /etc/ssl
- /etc/resolv.conf
- /usr/share/zoneinfo
- Any sockets you need, e.g /var/run/mysqld/mysqld.sock

### ex

```sh
# Restrict access to potential sensitive data (kernels, config, mount points,
# private keys). The paths will be created if they don't exist and they must
# not be files.
TemporaryFileSystem=/boot:ro
TemporaryFileSystem=/etc/luks:ro
TemporaryFileSystem=/etc/ssh:ro
TemporaryFileSystem=/etc/ssl/private:ro
TemporaryFileSystem=/media:ro
TemporaryFileSystem=/mnt:ro
TemporaryFileSystem=/run:ro
TemporaryFileSystem=/srv:ro
TemporaryFileSystem=/var:ro
# Permit syslog(3) messages to journald
BindReadOnlyPaths=/run/systemd/journal/dev-log
```

In the example this is used with `BindReadOnlyPaths` to permit logging to syslog. To give write access to sub-directories use `BindPaths` in combination with `ReadWritePaths`.

## ProtectKernelLogs

- [freedesktop.org](https://www.freedesktop.org/software/systemd/man/systemd.exec.html#ProtectKernelLogs=)
- **deny access to the kernel log ring buffer**
- turn this on for services that do not need read/write access to the kernel log ring buffer
- **low impact, low risk**
- `/proc/kmsg`
- `/dev/kmsg`

If true, access to the kernel log ring buffer will be denied. Enabling this option removes `CAP_SYSLOG` from the capability bounding set for this unit, and installs a system call filter to block the syslog system call (not to be confused with the libc API syslog for userspace logging). 

The kernel exposes its log buffer to userspace via `/dev/kmsg` and `/proc/kmsg`. If enabled, these are made inaccessible to all the processes in the unit.

```sh
CapabilityBoundingSet=~CAP_SYSLOG
```

### verify

- could not verify

```sh
sudo systemd-run \
--pty \
--collect \
--service-type=oneshot \
--property=PrivateUsers=true \
--property=ProtectKernelLogs=true \
journalctl -k --since today -f

systemd-run \
--user \
--pty \
--collect \
--service-type=oneshot \
--property=PrivateUsers=true \
--property=ProtectKernelLogs=true \
journalctl --user -f
```

## Paths: inaccessible, readwrite, readonly, exec, noexec

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#ReadWritePaths=)
* `InaccessiblePaths`
* `ReadWritePaths`
    * cannot be used to gain write access to a file system whose superblock is mounted read-only
    * will not have effect if a process is missing the normal file permissions or ownership
* `ReadOnlyPaths`
  Nest `ReadWritePaths=` inside of `ReadOnlyPaths=` in order to provide writable subdirectories within read-only directories.
* `ExecPaths`
* `NoExecPaths`
* prefixes:
    * prefixed with `-`: ignored when they do not exist
    * prefixed with `+`: paths are taken relative to the root directory of the unit

Note that the effect of these settings may be undone by privileged processes. In order to set up an effective sandboxed environment for a unit it is thus recommended to combine these settings with one of:

```sh
CapabilityBoundingSet=~CAP_SYS_ADMIN
SystemCallFilter=~@mount
```

### ReadWritePaths

Paths listed in `ReadWritePaths=` are accessible from within the namespace with the same access modes as from outside of it.

### ReadOnlyPaths

Paths listed in `ReadOnlyPaths=` are accessible for reading only, writing will be refused even if the usual file access controls would permit this.

Nest `ReadWritePaths=` inside of `ReadOnlyPaths=` in order to provide writable subdirectories within read-only directories.

### InaccessiblePaths

- **nesting exceptions not possible**:
  it is not possible to nest exceptions  inside (like `ReadWritePaths=`). For a more flexible option, see `TemporaryFileSystem=`.

Paths listed in `InaccessiblePaths=` will be made inaccessible for processes inside the namespace along with everything below them in the file system hierarchy.

Paths listed in `InaccessiblePaths=` will be made inaccessible for processes inside the namespace along with everything below them in the file system hierarchy. 

### example

```sh
# Simple allow-list example using these directives:
[Service]
ReadOnlyPaths=/
ReadWritePaths=/var /run
InaccessiblePaths=-/lost+found
NoExecPaths=/
ExecPaths=/usr/sbin/my_daemon /usr/lib /usr/lib64
```

```sh
InaccessiblePaths=-/etc/letsencrypt # The minus in front of the path tells systemd not to raise an error if the path doesn’t exist at runtime.

strace -e trace=%file <program> # identify what paths a program requires
```

# CAPABILITIES

Linux kernel capabilities are per-process thread security policies that control access to specific kernel features


```sh
systemd-analyze capability # lists all possible options
```

### ProtectClock

* https://linux-audit.com/systemd/settings/units/protectclock/
* Implementation risk: very low
* **prevent a service from making any changes to the system clock**
* removes `CAP_SYS_TIME` and `CAP_WAKE_ALARM` from the capability bounding set for this unit
* `/dev/rtc0`, `/dev/rtc1`, etc. are made read-only to the service

Most processes should only allowed to read clock information, but not modify it. The obvious exception to this is a service like a NTP daemon or program such as rdate. For most services it is therefore safe to prevent the service attempting to make changes to the system clock.

```systemd
[Service]
ProtectClock=yes
```

### CapabilityBoundingSet

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#CapabilityBoundingSet=)
* https://man7.org/linux/man-pages/man7/capabilities.7.html

Capabilities listed will be included in the bounding set, all others are removed.

```sh
CapabilityBoundingSet=CAP_SYS_PTRACE # add
CapabilityBoundingSet=~CAP_SYS_PTRACE # remove
```

```sh
CapabilityBoundingSet=CAP_A CAP_B
CapabilityBoundingSet=CAP_B CAP_C
# CAP_A, CAP_B, and CAP_C are set
```

```sh
CapabilityBoundingSet=CAP_A CAP_B
CapabilityBoundingSet=~CAP_B CAP_C
# only CAP_A is set.
```

#### verify

```sh
# run as root:
sudo systemd-run \
--pty \
--collect \
--service-type=oneshot \
--property=PrivateUsers=true \
--property=ProtectKernelLogs=true \
--property=CapabilityBoundingSet=~CAP_DAC_OVERRIDE \
bash

# in another terminal:
sudo pscap | grep bash # CAP_DAC_OVERRIDE is missing
```
### AmbientCapabilities

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#AmbientCapabilities=)
* **highly relevant for running software that uses an interpreter**
  
```sh
AmbientCapabilities= # Prevent any child processes from inheriting their parent’s capabilities
```

# SYSTEM CALLS

We can also block or allow-list specific system calls (syscall) or sets of syscalls. This gives an even more granular control than kernel capability sets.

## systemd and seccomp

**seccomp** syscall affects the calling process and its children, but there is no interface to inject the policy from “outside”. It is expected that developers will sandbox their code themselves as part of the application logic

Also, most real-world software is usually written using some high-level programming language and/or a framework, where the developers do not deal with the system calls directly and probably are even unaware which system calls are being used by their code.

**developers have the ability to sandbox their code, but are usually not incentivized to do so and operators have the incentive to sandbox the code, but don’t have the ability.**

Systemd is one of the popular implementations of a “zero code seccomp” approach

> ...Note that the `execve, exit, exit_group, getrlimit, rt_sigreturn, sigreturn` system calls and the system calls for querying time and sleeping are implicitly whitelisted and do not need to be listed explicitly...

This is mostly related to the way how systemd manages processes and injects the seccomp policy. So, to inject the policy, systemd forks itself, calls seccomp in the forked process and then execs the forked process into the target application. That’s why always allowing the `execve` system call is necessary in the first place, because otherwise systemd cannot do its job as a service manager.

## SystemCallArchitectures

- **Disabling non-native syscalls**

Non-native binaries, in almost all cases 32-bit binaries, may partially compromise the security of the system because they do not have access to more hardening. There have been some relatively minor vulnerabilities, like CVE-2009-0835, which affected non-native syscalls. 

**Warning:** This **will** break 32-bit binaries. Trying to execute such a binary will result in a file not found error.

```sh
# sudo nano /etc/systemd/system.conf
SystemCallArchitectures=native
```

## RestrictNamespaces

* https://linux-audit.com/kernel/namespaces/
* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#RestrictNamespaces=)
* Implementation risk: low
* **Restricts access to Linux namespace functionality for the processes of this unit**
* limits access to
    * `clone`
    - `setns`
    - `unshare`

Look in the source code for these syscalls, run `strace` or inspect a binary.

```sh
strings /lib/systemd/systemd-udevd | grep -E "(clone|setns|unshare)"
setns
Failed to clone sd_device object: %m
```

## RestrictAddressFamilies

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/systemd.exec.html#RestrictAddressFamilies=)
* limit exposure of processes to remote access, in particular via exotic and sensitive network protocols, such as `AF_PACKET`
* In most cases, the local `AF_UNIX` address family should be included in the configured allow list as it is frequently used for local communication
* When prefixed with "`~`" the listed address families will be applied as deny list, otherwise as allow list. 
* restricts access to the `socket` system call

```sh
RestrictAddressFamilies=none # all address families will be denied
RestrictAddressFamilies=AF_UNIX AF_INET AF_INET6
```

Restricts the set of socket address families accessible to the processes of this unit. Takes a space-separated list of address family names to whitelist, such as `AF_UNIX`, `AF_INET` or `AF_INET6`. When prefixed with `~` the listed address families will be applied as blacklist, otherwise as whitelist. Note that this restricts access to the socket system call only. Sockets passed into the process by other means (for example, by using socket activation with socket units, see systemd.socket are unaffected. Also, sockets created with `socketpair()` (which creates connected AF_UNIX sockets only) are unaffected. Note that this option has no effect on 32-bit x86 and is ignored (but works correctly on x86-64). If running in user mode, or in system mode, but without the `CAP_SYS_ADMIN` capability (e.g. setting `User=nobody`), `NoNewPrivileges=yes` is implied. By default, no restriction applies, all address families are accessible to processes. If assigned the empty string, any previous list changes are undone. Use this option to limit exposure of processes to remote systems, in particular via exotic network protocols. Note that in most cases, the local `AF_UNIX` address family should be included in the configured whitelist as it is frequently used for local communication, including for syslog logging. This does not affect commands prefixed with `+`.

## KeyringMode

* https://linux-audit.com/systemd/settings/units/keyringmode/
* Implementation risk: (very low)

The kernel keyring provides key material to services, such as security data, encryption keys, and authentication information. If a service does not need access to the keyring of a particular user (including root), then systemd allows restricting this using the KeyringMode setting.

When in doubt that key material is requested by a service, inspect the program code or use `strace` to track the following syscalls:

- `add_key`
- `request_key`

If no access is needed to key material, then lock access down.

```bash
[Service]
KeyringMode=private
```

## LockPersonality

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/systemd.exec.html#LockPersonality=)
* **prevents changing the “process execution domain”** 
  (`man 2 personality`), a rarely used feature with potential bugs

Locks down the [personality](http://man7.org/linux/man-pages/man2/personality.2.html) system call so that the kernel execution domain may not be changed from the default or the personality selected with `Personality=` directive.

## MemoryDenyWriteExecute

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/systemd.exec.html#MemoryDenyWriteExecute=)
* incompatible with programs/libraries that generate program code dynamically at runtime
* **makes it harder for software exploits to change running code dynamically**
* **prevents memory mappings which are both writable and executable**

If set, attempts to create memory mappings that are writable and executable at the same time, or to change existing memory mappings to become executable, or mapping shared memory segments as executable are prohibited. Specifically, a system call filter is added that rejects [mmap](http://man7.org/linux/man-pages/man2/mmap.2.html) system calls with both `PROT_EXEC` and `PROT_WRITE` set, [mprotect](http://man7.org/linux/man-pages/man2/mprotect.2.html) or [pkey_mprotect](http://man7.org/linux/man-pages/man2/pkey_mprotect.2.html) system calls with `PROT_EXEC` set and [shmat](http://man7.org/linux/man-pages/man2/shmat.2.html) system calls with `SHM_EXEC` set. Note that this option is incompatible with programs and libraries that generate program code dynamically at runtime, including JIT execution engines, executable stacks, and code "trampoline" feature of various C compilers. This option improves service security, as it makes harder for software exploits to change running code dynamically. 

However, **the protection can be circumvented, if the service can write to a filesystem, which is not mounted with noexec** (such as /dev/shm), or it can use memfd_create(). 

This can be prevented by making such file systems inaccessible to the service (e.g. `InaccessiblePaths=/dev/shm`) and installing further system call filters (`SystemCallFilter=~memfd_create`).

```sh
MemoryDenyWriteExecute=true
InaccessiblePaths=/dev/shm
SystemCallFilter=~memfd_create
```

## SystemCallFilter

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#SystemCallFilter=)
* **allow-listing**
* **deny-listing**: If the first character of the list is "`~`". Then the effect is inverted: only the listed system calls will result in immediate process termination
  `SystemCallFilter=~@mount`

Note that the `execve()`, `exit()`, `exit_group()`, `getrlimit()`, `rt_sigreturn()`, `sigreturn()` system calls and the system calls for querying time and sleeping are implicitly allow-listed and do not need to be listed explicitly.
  
```sh
SystemCallFilter=@system-service # Set that contains some common syscalls used by services. It notably removes the ability to reboot the system, interfere with swap memory, and change the system clock. Few services need these capabilities.
```

```sh
# relatively safe basic choice for the majority of system services:
SystemCallFilter=@system-service
SystemCallErrorNumber=EPERM
```

```sh
SystemCallFilter=
SystemCallFilter=@system-service
SystemCallFilter=~@aio @chown @clock @cpu-emulation @debug @keyring @memlock @module @mount @obsolete @privileged @raw-io @reboot @resources @setuid @swap userfaultfd mincore
```

```sh
SystemCallFilter=~@privileged # System calls related to super-user
SystemCallFilter=~@resources
```
### prevent process from undoing ns mappings

```sh
SystemCallFilter=~@mount # It is recommended to combine the file system namespacing related options with this in order to prohibit the unit's processes to undo the mappings. 
```

Recommended for:

- `PrivateTmp=`
- `PrivateDevices=`
- `ProtectSystem=`
- `ProtectHome=`
- `ProtectKernelTunables=`
- `ProtectControlGroups=`
- `ProtectKernelLogs=`
- `ProtectClock=`
- `ReadWritePaths=`
- `ReadOnlyPaths=`
- `InaccessiblePaths=`
## SystemCallErrorNumber

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#SystemCallErrorNumber=)

Takes an "`errno`" error number (between 1 and 4095) or errno name such as `EPERM`, `EACCES` or `EUCLEAN`, to return when the system call filter configured with `SystemCallFilter=` is triggered, instead of terminating the process immediately. See [errno(3)](https://man7.org/linux/man-pages/man3/errno.3.html) for a full list of error codes. 

### kill process

```sh
# When this setting is not used, or when the following values are set, the process will be terminated immediately when the filter is triggered:
SystemCallErrorNumber=
SystemCallErrorNumber=kill
```

## SystemCallLog

* [freedesktop.org](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#SystemCallLog=)

Takes a space-separated list of system call names. If this setting is used, all system calls executed by the unit processes for the listed ones will be logged. If the first character of the list is "`~`", the effect is inverted: all system calls except the listed system calls will be logged.

