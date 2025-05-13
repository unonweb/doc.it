# dmesg

```sh
#### [output]
# Werte wie [2990.314477] geben an nach wie vielen Sekunden.Mikrosekunden ein Ereignis eingetreten ist.
dmesg -H
dmesg --human # enable human-readable output

#### [filter]

dmesg -l 
dmesg --level <level> # restricts output to the given (comma-separated) list of levels.
dmesg --level=err,warn # will print error and warning messages only

dmesg -f
dmesg --facility <facility> # restricts output to the given (comma-separated) list of facilities.
dmesg --facility=daemon # will print messages from system daemons only

#### [console]

dmesg -n
dmesg --console -level <level> # sets the level at which printing of messages is done to the console;

dmesg -n 1 
dmesg -n alert # prevents all messages, except emergency (panic) messages, from appearing on the console.  All levels of messages are still written to /proc/kmsg, so syslogd can still be used to control exactly where kernel messages appear. When the -n option is used, dmesg will not print or clear the kernel ring buffer.

dmesg -E
dmesg --console -on # enables printing messages to console
dmesg -D
dmesg --console -off # disables the printing of messages to the console.
```
