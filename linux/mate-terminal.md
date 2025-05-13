# mate-terminal

```sh
mate-terminal \
	--tab --title=$title1 --command "bash -c '$cmd1; bash'" \
	--tab --title=$title2 --command "bash -c '$cmd2; bash'"
```

```sh
mate-terminal --command # Execute the argument to this option inside the terminal; mate-terminal -e
mate-terminal --execute # Execute the remainder of the command line inside the terminal; mate-terminal -x
```

## style 1: `bash --rcfile`

```sh
mate-terminal --command  "bash --rcfile cmd.sh "

# --rcfile <file> # Execute commands from file instead of the system wide initialization file /etc/bash.bashrc and the standard personal initialization file ~/.bashrc if the shell is interactive
```

where cmd.sh is

```
#!/bin/bash
CMD="ls -lh ."
history -s "$CMD"
$CMD
```

## style 2: `bash -c`

```sh
# bash -c If the -c option is present, then commands are read from the first non-option argument command_string.  If there are arguments after the command_string, the first argument is assigned to $0 and any remaining arguments are assigned to the positional parameters. The assignment to $0 sets the name of the shell, which is used in warning and error messages.
```
