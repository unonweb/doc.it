
# LINKS

+ [[systemd-nspawn]]
+ https://en.wikipedia.org/wiki/Linux_namespaces
+ Michael Kerrisk:
  https://www.youtube.com/watch?v=83NOk8pmHi8
  https://lwn.net/Articles/531114/
+ Ignat Korchagin
  Linux user namespaces: a blessing and a curse
  https://www.youtube.com/watch?v=UfoT88iE-TY
- What's in a Name? - Linux Namespaces 
  https://www.youtube.com/watch?v=dunL7EXDRKs

# CLI
## create: unshare

run program in new namespaces

creates new namespaces (as specified by the command-line options described below) and then executes the specified program. If program is not given, then `${SHELL}` is run

```sh
unshare [options] [program [arguments]]
```

## nsenter

- executes cmds in the namespace(s) that are specified in the command-line options
- execute cmds inside containers, without needing to install or use the Docker CLI

```sh
# enter all available namespaces
# keep the same user UID/GID mapping as the target process
sudo nsenter --all --target 3279 --preserve-credentials /bin/bash
```

```sh
nsenter [options] [program [arguments]]
nsenter --all
nsenter -t
nsenter --target <PID> # Specify a target process to get contexts from.

nsenter --mount
nsenter --uts
nsenter --ipc
nsenter --net

nsenter --user
nsenter -G
nsenter --setgid <gid> # Set the group ID which will be used in the entered namespace and drop supplementary groups. nsenter always sets GID for user namespaces, the default is 0.
nsenter --setuid <uid> # Set the user ID which will be used in the entered namespace. nsenter always sets UID for user namespaces, the default is 0.
nsenter --preserve-credentials # Don’t modify UID and GID when enter user namespace.
# Keep the same user UID/GID mapping as the target process.
# The default is to drops supplementary groups and sets GID and UID to 0.
# This enables privileged operations.

# ...

sudo nsenter --target 2525 --mount ls /
```

- Omitting a namespace will fall back to the host namespace for that resource.

## lsns

```sh
sudo lsnsn --tree #
sudo lsns -p <pid> # List namespaces for a process
```

```sh
findmnt -N <pid> # Use alternative namespace /proc/<tid>/mountinfo rather than the default /proc/self/mountinfo
findmnt -N 3308 --list | less
cat /proc/3308/mountinfo
```
## /proc/PID/ns

```sh
ll /proc/$$/ns/user # /proc/4274/ns/user -> 'user:[4026531837]'
```

# NAMESPACES

**A namespace wraps some global system resource to provide resource isolation**.

+ **restrict a process's view of the rest of the host**
+ partition kernel resources
+ a process or process group is associated with a namespace and can only see or use the resources associated with that namespace
+ Which resource is isolated depends on the kind of namespace

A Linux system begins with a single namespace of each type, used by all processes. Processes can create additional namespaces and can also join different namespaces.

Ein Prozess erzeugt die Namensräume, indem er einen Systemaufruf namens `unshare()` absetzt. Das trennt den aufrufenden Prozess von seinem existierenden Namespace und erzeugt einen neuen. Ein Prozess kann zudem den `setns()` -Aufruf anwenden, um einen auf dem System existierenden Namensraum zu modifizieren.

Since kernel version 5.6, there are **8 kinds of namespaces**.

| Namespace | Funktion                                                             |
| --------- | -------------------------------------------------------------------- |
| IPC       | System-V-Interprozesskommunikation, Posix-Nachrichten-Warteschlangen |
| Network   | Netzwerkgeräte, Stacks, Ports und so weiter                          |
| Mount     | Mountpoints                                                          |
| PID       | Prozess-IDs                                                          |
| User      | Benutzer- und Gruppen-IDs                                            |
| UTS       | Hostname und NIS-Domainname                                          |
# USER NS

**The process will have a distinct set of UIDs, GIDs and capabilities** 

- provide both privilege isolation and user ID segregation
    - user and group IDs
    - the root directory
    - keys
    - capabilities
- available since kernel 3.8
- the only namespace type not requiring the `CAP_SYS_ADMIN` capability, allowing unprivileged users to create this namespace type
- main objective: **run unprivileged containers**

A process's user and group IDs can be different inside and outside a user namespace. In particular, a process can have a normal unprivileged user ID outside a user namespace while at the same time having a user ID of 0 inside the namespace; in other words, the process has full privileges for operations inside the user namespace, but is unprivileged for operations outside the namespace.

```sh
unshare --user[=file] # Create a new user namespace

unshare --keep-caps # When the --user option is given, ensure that capabilities granted in the user namespace are preserved in the child process.
```
## mapping

```sh
unshare -r
unshare --map-root-user # Run the program only after the current effective user and group IDs have been mapped to the superuser UID and GID in the newly created user namespace. 
# This makes it possible to conveniently gain capabilities needed to manage various aspects of the newly created namespaces (such as configuring interfaces in the network namespace or mounting filesystems in the mount namespace) even when run unprivileged. 
# As a mere convenience feature, it does not support more sophisticated use cases, such as mapping multiple ranges of UIDs and GIDs. This option implies --setgroups=deny and --user. 
# This option is equivalent to 
unshare --map-user=0 --map-group=0
```

```sh
unshare -c
unshare --map-current-user # Run the program only after the current effective user and group IDs have been mapped to the same UID and GID in the newly created user namespace. This option implies --setgroups=deny and --user.
# This option is equivalent to 
unshare --map-user=$(id -ru) --map-group=$(id -rg)
```

## example

```sh
unshare --fork --pid --mount-proc --user --map-root-user bash 
# opens a new shell as root (but not really)
```

## unprivileged user namespaces

- https://www.youtube.com/watch?v=UfoT88iE-TY

```sh
unshare --uts bash # Operation not permitted
unshare --user bash # works
```

### controversy

Unprivileged Linux user namespaces is a rather controversial topic in the security community. 

On one side it allows building unprivileged and sandboxed services and applications, which would otherwise require elevated privileges to successfully run and provide features to their users. Not granting privileges to such applications follows the least privilege principle and makes our systems more secure. 

On the other side, this mechanism has been repeatedly used in various vulnerabilities and exploits as a starting attack vector, multiplying the damage and impact of these exploits. And since it became so popular within the offensive industry, many Linux distributions and security guidances started recommending disabling this feature altogether.
### disable

```sh
kernel.unprivileged_userns_clone=0 
user.max_user_namespaces=0
```

Disabling unprivileged user namespaces breaks:

- bubblewrap
- flatpak
- AppImages
- docker?
- popular browser sandboxes

# PID

**Isolate PID number space**. Children will have a distinct set of PID-to-process mappings from their parent
  
Befindet sich ein Prozess beispielsweise in seinem eigenen PID-Namespace, sieht er nur andere Prozesse auf dem System, die sich im selben Namespace befinden. Auf diese Weise verhindern die Anwender, dass die Prozesse über verschiedene Achsen miteinander interagieren.

```sh
unshare --pid[=file] # Create a new PID namespace. Creation of a persistent PID namespace will fail if the --fork option is not also specified.

unshare --fork # Fork the specified program as a child process of unshare rather than running it directly. This is useful when creating a new PID namespace.

unshare --mount-proc[=mountpoint] # Just before running the program, mount the proc filesystem at mountpoint (default is /proc). This is useful when creating a new PID namespace. It also implies creating a new mount namespace since the /proc mount would otherwise mess up existing programs on the system.
```

```sh
sudo unshare --fork --pid --mount-proc ps -aux
# create a PID namespace
# --fork the executed command is performed in a child process that (being the first process in the namespace) has PID 1. 
# --mount-proc option ensures that a new mount namespace is also simultaneously created and that a new proc filesystem is mounted that contains information corresponding to the new PID namespace.
```

# NETWORK

 The process will have independent IPv4 and IPv6 stacks, IP routing tables, firewall rules, the `/proc/net` and `/sys/class/net` directory trees, sockets, etc
 
```sh
unshare --net[=file] # Create a new network namespace
# If <file> is specified, then the namespace is made persistent by creating a bind mount at file
```

# CGROUP

The process will have a virtualized view of `/proc/self/cgroup`, and new cgroup mounts will be rooted at the namespace cgroup root

# MOUNT

The process will only see a certain set of mountpoints. 

Mounting and unmounting filesystems will not affect the rest of the system, except for filesystems which are explicitly marked as shared (with `mount --make-shared`)

# UTS
 
**Setting hostname or domainname will not affect the rest of the system**

```sh
sudo unshare -u
sudo unshare --uts[=file] # Create a new UTS namespace
```

```sh
sudo unshare --uts bash
hostname new_hostname # only sets the new name in this ns
```
# IPC

The process will have an independent namespace for POSIX message queues as well as System V message queues, semaphore sets and shared memory segments

# TIME

The process can have a distinct view of CLOCK_MONOTONIC and/or CLOCK_BOOTTIME which can be changed using /proc/self/timens_offsets