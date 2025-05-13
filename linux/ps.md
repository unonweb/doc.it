
# SOLUTIONS

## kill

```sh
## kill all processes of the same group
pgrep -fa eleventy
# 64441 npm exec eleventy --config=/home/frida/code/web/projects/sites/rjuschka/11ty/eleventy.cjs --watch --incremental
kill 64441 # only kills the given pid
kill -- -64441 # kills all processes of the same group (double dash needed because argument starts with dash)
```

## user

```sh
## to which user belongs process XY?
systemctl --user status un-payloadcms.service | grep "Main PID" # get pid of service
ps -f 7037 # get user of process

ps -axu | grep apache2
```

## useful examples

```sh
ps -o "pid, user, %mem, command" -ax | sort -b -k3 -r | grep strapi
# -o define output format

ps -eo rss,command --sort -rss | grep strapi | awk '{ hr=$1/1024 ; printf("%13.2f Mb ",hr) } { for ( x=4 ; x<=NF ; x++ ) { printf("%s ",$x) } print "" }'


ps afu | grep strapi | awk 'NR>1 {$5=int($5/1024)"M";}{ print;}'
# takes the result from ps 
# then for every line (except the first one) it replacecs 5th column to MB adding M suffix

ps -e -o 'pid ppid command' | grep 'eleventy'
```

# COMBOS

Commands options such as ps -aux are not recommended as it is a confusion of two different standards. According to the POSIX and UNIX standards, the above command asks to display all processes with a TTY (generally the commands users are running) plus all processes owned by a user named "x". If that user doesn't exist, then ps will assume you really meant "ps aux".

```sh
ps -elf bzw. -elF
ps -e bzw. -A # selects all processes
ps -l # long format
ps -f bzw. -F # full format bzw. extra full format 

ps -aux
ps -a # zeigt Prozesse aller Benutzer, solange sie mit einem Terminal verbunden sind.
ps -u # +[Startzeit] +[Pfad zur ausführbaren Datei] +[ausführender Benutzer]
ps -x # zeigt auch Prozesse, die nicht mit einem Terminal verbunden sind (z.B. daemons)
```

# SELECTION

```sh
ps -C <command> # Ausgabe aller Instanzen von command 
ps -u <user> # zeigt nur Prozesse von user
ps -p <pid>

# [simple selection]
ps -a # all processes except both session leaders and processes not associated with a terminal
ps -e # all processes; identical to -A
ps -r # only running processes
ps --deselect # Select all processes except those that fulfill the specified conditions
```

```sh
# to which user belongs process XY?
systemctl status un-payloadcms.service | grep "Main PID" # get pid of service
ps -f 7037 # get user of process
```

# OUTPUT

```sh
ps -f # full-format listing; can be combined with many other options to add additional columns
UID  PID  PPID  C  STIME  TTY  TIME  CMD

ps -F # extra full format
UID  PID  PPID  C  SZ  RSS  PSR  STIME  TTY  TIME  CMD

ps -l # long format
F  S  UID  PID  PPID  C  PRI  NI  ADDR  SZ  WCHAN  TTY  TIME  CMD

ps -u # +[Startzeit] +[Pfad zur ausführbaren Datei] +[ausführender Benutzer]
USER  PID  %CPU  %MEM  VSZ  RSS  TTY  STAT  START  TIME  COMMAND
```

## custom format

```sh
ps -o format # user-defined format; a way to specify individual output columns; separator: whitespace or comma; 
ps -O format # Like -o, but preloaded with some default columns. 
Identical to -o pid,format,state,tname,time,command
```

```sh
ps -eo 'pid ppid command' | grep 'eleventy'
sudo ps  --forest -eo 'pid command' | grep 'signal'
```

```sh
args|cmd # command with all its arguments as a string.
comm # command name (only the executable name)


# memory #

rss # resident set size, the non-swapped physical memory that a task has used (in kilobytes)
trs # text resident set size, the amount of physical memory devoted to executable code.
vsz # virtual memory size of the process in KiB (1024-byte units). Device mappings are currently excluded; this is subject to change.
%mem # ratio of the process's resident set size to the physical memory on the machine, expressed as a percentage
size # approximate amount of swap space that would be required if the process were to dirty all writable pages and then be swapped out.  This number is very rough!

```

## output modifiers

```sh
ps --sort key # increasing numerical or lexicographic order
ps --sort -key # decreasing numerical or lexicographic order
ps --headers # Repeat header lines, one per page of output.
ps --forest # Show process hierarchy by ASCII art process tree
ps -H # Show process hierarchy by indentation
```
