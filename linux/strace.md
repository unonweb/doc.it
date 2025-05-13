# STRACE

**trace system calls and signals;** intercepts and records the system calls which are called by a process and the signals which are received by a process.

```sh
strace [options] <command> [arguments]
```

Each line in the trace contains the system call name, followed by its arguments in parentheses and its return value

```sh
strace cat /dev/null

open("/dev/null", O_RDONLY) = 3
Errors (typically a return value of -1) have the errno symbol and error string appended.
open("/foo/bar", O_RDONLY) = -1 ENOENT (No such file or directory)
```

## options

```sh
strace -e [qualifier][!]value
# The default qualifier is trace
strace -e open # means literally 
strace -e trace=open # which in turn means trace only the open system call
```

### qualifiers
```
strace -e <syscall_set>
strace -e trace=<syscall_set>
strace --trace=<syscall_set>
```

Trace only the specified set of system calls. _syscall_set_ is defined as [!]_value_[,_value_], and value can be one of the following:

```sh
_syscall_ # Trace specific syscall, specified by its name

%file # Trace all system calls which take a file name as an argument. You can think of this as an abbreviation for -e trace=open,stat,chmod,unlink,... which is useful to seeing what files the process is referencing.

%process # Trace all system calls which involve process management. This is useful for watching the fork, wait, and exec steps of a process.

%net # Trace all the network related system calls.

%signal # Trace all signal related system calls.

%ipc # Trace all IPC related system calls.

%desc # Trace all file descriptor related system calls.

%memory # Trace all memory mapping related system calls.

%creds # Trace system calls that read or modify user and group identifiers or capability sets.
```

```sh
strace -e status=<set>
strace --status=<set> #
# Print only system calls with the specified return status. As strace waits for system calls to return before deciding whether they should be printed or not, the traditional order of events may not be preserved anymore.

successful # Trace system calls that returned without an error code.

failed # Trace system calls that returned with an error code.

```


```sh
strace -P
strace --trace-path <path> # Trace only system calls accessing path. Multiple -P options can be used to specify several paths.
```

### output format

```sh
strace -v
strace --no-abbrev # Print unabbreviated versions of environment, stat, termios, etc. calls.
```

### statistics

```sh
strace -c
strace --summary-only <command> # counts time, calls, errors for each system call and report a summary on program exit, suppressing the regular output.
```