# SYSTEM CALLS

A system call is a programmatic way a program requests a service from the kernel. One of the main functions of an operating system is to provide abstractions to user programs.

System calls are very similar to function calls, which means they accept and work on arguments and return values. The only difference is that system calls enter a kernel, while function calls do not.

```sh
man syscalls # info about syscalls in general
man 2 <syscall> # info about _syscall_
```

Program invokes functions from system libraries (glibc)
-> _**Invokes system calls**_

```sh
ltrace <command> # ...if you want to know which functions were called from the glibc library
```

## socket

create an endpoint for communication

```c
socket(domain, type, protocol)
# On success, a file descriptor for the new socket is returned. 
# On error, -1 is returned, and _errno_ is set appropriately.
```

- **domain** 
  specifies a communication domain; this selects the protocol family which will be used for communication.
    - AF_UNIX # Local communication
    - AF_INET # IPv4 Internet protocols
    - AF_NETLINK # Kernel user interface device
- **type**
  specifies the communication semantics
    - SOCK_STREAM # Provides sequenced, reliable, two-way, connection-based byte streams.
    - SOCK_DGRAM # Supports datagrams (connectionless, unreliable messages of a fixed maximum length).
    - SOCK_RAW # Provides raw network protocol access.
- **protocol**
  specifies a particular protocol to be used with the socket.

### access / faccessat

check user's permissions for a file

```
access(pathname, mode)
faccessat(dirdf, pathname, mode, flags)
```
