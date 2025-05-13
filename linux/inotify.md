# inotify

```sh
sudo apt install inotify-tools
```

# inotifywait

efficiently waits for changes to files using Linux's inotify interface. It is suitable for waiting for changes to files from shell scripts. It can either exit once an event occurs, or continually execute and output events as they occur.

```sh
--monitor # Instead of exiting after receiving a single event, execute indefinitely. The default behaviour is to exit after the first event occurs.
# instead of exiting after receiving a single event (that is the default), inotifywait executes indefinitely because of the –monitor flag. This is an impressive boost of performance compared to restarting inotifywait after every event.
--daemon # Same as --monitor, except run in the background logging events to a file that must be specified by --outfile. Implies --syslog.
--event <event> # Listen  for  specific  event(s)  only.
```

```sh
#!/bin/bash

# Set the local and remote directories
local_dir="/path/to/local"
remote_dir="user@host_or_ip:/path/to/remote"
# Set the rsync options
rsync_options="-az  --progress "
# Start the loop to monitor for changes
while true; do
    # Use the inotifywait command to monitor for file changes in the local directory
    inotifywait -r -e modify,create,delete,move ${local_dir}
    # Trigger rsync to copy all changed files from local to remote
    # echo rsync ${rsync_options} ${local_dir}/ ${remote_dir}
    rsync ${rsync_options} -e 'ssh -p 22' ${local_dir}/ ${remote_dir}
done
```

#### ex baeldung.com

```bash
#!/bin/bash
 
if [ -z "$(which inotifywait)" ]; then
    echo "inotifywait not installed."
    echo "In most distros, it is available in the inotify-tools package."
    exit 1
fi
 
counter=0;
 
function execute() {
    counter=$((counter+1))
    echo "Detected change n. $counter" 
    eval "$@"
}
 
inotifywait --recursive --monitor --format "%e %w%f" --event modify,move,create,delete ./ | while read changed; do
    echo $changed
    execute "$@"
done
```

- **anything following the script name is interpreted as the command to be executed**, thanks to the double-quoted [*$@* special variable](https://www.baeldung.com/linux/bash-special-variables) passed to the *execute()* [function](https://www.baeldung.com/linux/bash-functions)
- each line of output from *inotifywait* (formatted as specified by the [*–format*](https://linux.die.net/man/1/inotifywait) flag) is temporarily stored in the variable *$changed*, thanks to the [*pipe*](https://www.baeldung.com/linux/pipes-redirection#piping) between the *inotifywait* command and the [*while read*](https://www.baeldung.com/linux/values-into-variable-from-pipe#using-read-with-a-while-loop) loop
- instead of exiting after receiving a single event (that is the default), ***inotifywait\* executes indefinitely because of the \*–monitor\* flag**. This is an impressive boost of performance compared to restarting *inotifywait* after every event.
- the *–event* flag specifies the [events](https://www.man7.org/linux/man-pages/man1/inotifywait.1.html#EVENTS) to be monitored in the current directory and subdirectories, watching recursively to an unlimited depth as requested by the *–recursive* flag
- newly created subdirectories will also be watched

#### ex man page

```sh
#!/bin/sh
# A short shell script to efficiently wait for httpd-related log messages and do something appropriate.
while ! inotifywait -e modify /var/log/messages; do
	if tail -n1 /var/log/messages | grep httpd; then
		kdialog --msgbox "Apache needs love!"
	fi
done
```
