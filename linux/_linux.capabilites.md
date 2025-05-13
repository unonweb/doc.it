
# CLI

```sh
sudo apt install libcap-ng-utils
sudo pscap
sudo filecap 
```

## pscap

```sh
sudo pscap
```

- If a process is not in the report, it has dropped all capabilities.
- If the process has partial capabilities, it is further examined to see if it has an open-ended bounding set. If this is found to be true, a `+` symbol is added.
- If the process has ambient capabilities, a `@` symbol is added.

## filecap

```sh
sudo filecap # prints out a report of programs with file based capabilities. If a file is not in the report or there is no report at all, no capabilities were found. For expedience, the default is to check only the directories in the PATH environmental variable. 
sudo filecap # To check file capabilities in $PATH
sudo filecap -a # all directories will be checked
sudo filecap <dir> #  recursively check that directory
sudo filecap <file> # only check that file

sudo filecap -d # To list all possible capabilities
sudo filecap /bin/ping net_raw net_admin # To set a file capability on a specific program
sudo filecap /bin/ping none # To remove file capabilities on a specific program
```

```sh
sudo filecap -a 2>/dev/null # show all the programs on the system that have had capabilities applied to them.

# set       file                 capabilities  rootid
# effective /var/lib/machines/debian/usr/bin/ping    net_raw
# effective /usr/bin/ping    net_raw
# effective /usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper    net_bind_service, net_admin
```
# BACKGROUND: setUID

Traditional UNIX privilege model divides users into two groups:
- Normal users, subject to privilege checking based on UID and GIDs
- Superuser (UID 0) bypasses many of those checks

Traditional mechanism for giving privilege to unprivileged users is **set-UID-root** program

```sh
chown root <user>
chmod u + s <prog>
```

- When executed, process assumes UID of file owner
  ⇒ process gains privileges of superuser
- Powerful, but dangerous
  ⇒ No limit on possible damage if program is **compromised**

**Problem: Say we want to give a program the power to change system time. We must also give it power to do everything else root can do.**

# CAPABILITIES

**A subset of power of root.** Capabilities are an attempt to solve this problem By dividing the  power of root into small pieces:

- 38 capabilities as at Linux 5.1
- Examples:
    - `CAP_DAC_OVERRIDE`: bypass all file permission checks
    - `CAP_SYS_ADMIN`: do (too) many different sysadmin operations
    - `CAP_SYS_TIME`: change system time
- Processes can have capabilities
- Files can have attached capabilities, which are given to process that executes program
- Privileged binaries/processes using capabilities are less dangerous if compromised

Instead of set-UID-root programs, we can attach one/a few capabilities.
When program is executed ⇒ process gets those capabilities

Attached using `setcap` (needs CAP_SETFCAP capability!)


## capabilities & system calls

Most processes that run as a daemon will require some of the available [Linux capabilities](https://linux-audit.com/kernel/capabilities/). Some developers define these capabilities clearly, but most of them don’t. In that case, we need to figure out what capabilities are required to operate correctly. 

As an extension to these capabilities, we have the [syscalls](https://linux-audit.com/kernel/syscalls/) that are used. These system functions allow the user space program to communicate with the kernel in a standardized way. To have a process working correctly, we need to make sure that it also can use the syscalls it requires, similarly to the capabilities. This is also where capabilities and syscalls come together, as usually the usage of syscalls give a very good hint on what capabilities are required.