
# LINKS

- https://www.kernel.org/doc/html/v4.17/userspace-api/seccomp_filter.html
- FOSDEM 2020: Philipp Krenn: seccomp — Your Next Layer of Defense
  https://www.youtube.com/watch?v=QfJtKOtXeiw

# SECCOMP

**secure computing** is a security facility in the Linux kernel. 

**seccomp allows a process to make a one-way transition into a "secure" state where it cannot make any system calls except `exit()`, `sigreturn()`, `read()` and `write()` to already-open file descriptors.** 

Should it attempt any other system calls, the kernel will either just log the event or terminate the process with SIGKILL or SIGSYS. In this sense, it does not virtualize the system's resources but isolates the process from them entirely.

# SECCOMP-BPF

- extension to seccomp
- allows **filtering of system calls** using a configurable policy implemented using **Berkeley Packet Filter** rules
- **reduce the total kernel surface exposed to the application**
- limit the amount of interaction a program can have with the underlying system
- meant to be a tool for sandbox developers to use

- used by 
- OpenSSH
- vsftpd
- Chromium