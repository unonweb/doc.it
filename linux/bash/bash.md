# LINKS

* https://www.gnu.org/software/bash/manual/html_node/Bash-Builtins.html

# TROUBLES



# GUIDES

## minimize the use of subshells

Avoid subshells when possible as they create child processes.

```bash
# subshell
files=$(ls *.txt)
for file in $files; do
    echo $file
done
```

```bash
# Direct approach
for file in *.txt; do
    echo $file
done
```

## enable nounset and exit on error

Make scripts more robust with error handling:  

```bash
set -u  # Treat unset variables as an error
set -e  # Exit on any error

function my_function() {
    echo "This is my function"
}
my_function
```

## process files in parallel

Use `xargs` to parallelize operations for better performance.

```bash
# sequential processing:  
for file in *.txt; do
    process_file "$file"
done
```

```bash
# parallel processing
ls *.txt | xargs -n 1 -P 4 process_file
```

# BEHAVIOUR

- https://www.gnu.org/software/bash/manual/bash.html#Modifying-Shell-Behavior

## solutions

- https://pythonspeed.com/articles/shell-scripts/

### Problem: errors don’t stop execution

**Solution**: add `set -e` to the top of the shell script

```bash
#!/bin/bash
set -e
touch newfile
cp newfil newfile2  # deliberate typo!
echo "Success" # is not printed because of set -e
```

### Problem: unknown variables cause no errors

 **Solution**: add `set -u` to the top of the shell script
 
```bash
#!/bin/bash
set -eu # with set -u bash catches the typo
export PATH="venv/bin:$PTH"  # Typo is deliberate
ls
```

### Problem: pipes don’t catch errors

**Solution**:  `set -o pipefail`

```bash
#!/bin/bash
set -euo pipefail
nonexistentprogram | echo
echo "Success!" # is not printed because of pipefail
```

### Problem: Subshells are weird

```bash
#!/bin/bash
set -euo pipefail
export VAR=$(echo hello | nonexistentprogram) # error is ignored
echo "Success!" # is printed
```

Errors in subshells aren’t treated as an error if they’re part of a command’s arguments. That means that subshell’s error just gets thrown away.

**solution**: set variable directly

```bash
#!/bin/bash
set -euo pipefail
VAR=$(echo hello | nonexistentprogram)
export VAR
echo "Success!" # not printed
```

## set

* https://www.gnu.org/software/bash/manual/bash.html#index-set
* despite its name, cannot be used to set/declare an environment variable
* can only print existing variables or set and unset shell specific options or shell positional parameters.

```sh
set # lists all existing shell variables (local and environment variables) when no argument is provided
set -k # exports all shell local variables to the environment. This is there for a legacy reason and should be avoided. Exporting only the necessary environment variables individually with declare or export should be preferred.
```

## shopt

- https://www.gnu.org/software/bash/manual/bash.html#The-Shopt-Builtin-1

# CONTROL CHARS

Steuerzeichen; Zeichen, die nicht direkt auf dem Bildschirm sichtbar sind, sondern eine Steuerfunktion haben.

## layout / control

* used by: `echo -e`, `printf`

```sh
\a **alert**

\b # backspace
\c # continue; unterdrückt die Ausgabe des newline-Zeichens
\f # form feed; einige Zeilen überspringen
\r # return; zurück zum Zeilenanfang
\n # new line; Zeilenumbruch
\t # tab horizontal (8 Leerzeichen)
\v # tab vertikal (8 Leerzeilen)

echo -e "hey\nyou"
echo -e "1.\tTomaten\n2.\tAugergine"
```

## ANSI Sequenzen

- Beginn der Sequenz: `\033[` bzw. `\e[`
- Dann beliebige Anzahl von Codes getrennt durch eine Semikolon
- Trennzeichen: `;`
- der **letzte** **Code** muss mit `m` abgeschlossen werden:

```sh
\e[ # start a sequence
\e[1m # make it bold
\e[0m # reset
```

### Texteigenschaften

gilt für `echo -e` und $PS1

```sh
echo -e "\e[1mDieser Text ist fett\e[0m"

\e[0 # alle Attribute und Farben zurücksetzen
\e[1 # fett
\e[2 # kursiv
\e[4 # unterstrichen
\e[5 # blinken
\e[7 # reverse

$'<ansi>' # wird von BASH ausgewertet
```

# EXECUTION

## solutions

```sh
bash -c "ls; pwd; <other commands...>"
```

## scripts

- die Endung "**.sh**" kann bei Skripten sicherstellen, dass es zu keinen Konflikten mit anderen Kommandos kommt

```sh
chmod u+x script.sh # make it executable
```

### shebang / hashbang

* tells the kernel what interpreter it needs to use to understand the language in this file, and where to find it
* files with a hashbang as their first line (and the executable permission) can be started by your system's kernel like any other program.

```sh
#! /bin/bash
#!/usr/bin/env bash
```

## bash

Each shell script running is, in effect, a subprocess (child process) of the parent shell.
A shell script can itself launch subprocesses. These subshells let the script do parallel processing, in effect executing multiple subtasks simultaneously.

```sh
#! /bin/bash
```

```sh
bash <file> # executes <file> in a new, separate process space, with a separate environment 
# same as execution via #!/bin/bash or ./file

./file # relative path to a script/programm in the current folder

bash -c <cmds>  # interprets the string argument command as a bash script and executes its content.
```

### options

```sh
bash -s # can be used to specify arguments
bash -s a b c # starts a shell... In this shell we can check arguments:
echo $1 $2 $2 # reflects the arguments from the bash command: a b c
# Without the '-s' option, the first positional argument would be interpreted as a file name:
# bash a b c # bash: a: No such file or directory
```

```sh
bash -v # prints shell input lines as they are read
bash -x # verbose debugging, prints commands and their arguments as they are executed
```

```sh
bash -c # commands are read from the first non-option argument <command_string>; if there are arguments after the <command_string>, the first argument is assigned to $0 and any remaining arguments are assigned to the positional parameters. The assignment to $0 sets the name of the shell, which is used in warning and error messages

bash -c <cmds> 
bash -c "ls; pwd;"
bash -c "ls;pwd; bash;" # 
```

#### --init-file

The default startup script is `~/.bashrc`, another script could be given with `--init-file`. 
If you simply pass `--init-file`, your script would replace the default, rather than augment it.

The solution is to pass, using the `<(...)` syntax, a temporary script that sources the default ~/.bashrc followed by any other commands:

```sh
bash --init-file <(echo ". ~/.bashrc; ls; pwd; ### other commands... ###")

echo "ls; pwd" > initfile
bash --init-file initfile
```

### restricted shell

```sh
bash -r
```

### interactive shell

**reads lines from you at the prompt**

```sh
bash -i
ps -ef | grep bash
# udnonner  1948  1947  0 13:58 pts/0    00:00:00 -bash # Das Minus markiert eine interaktive Shell
```

#### interactive login

```sh
bash -l # login-shell

sudo -i # or
sudo --login # or
su - <user> # öffnet eine Login-Shell; liest /etc/profile
```

#### interactive non-login

```sh
bash # öffnet eine interaktive non-login Subshell; liest /etc/bash.bashrc
```

### non-interactive

**reads commands from a file or stream**

* non-interactive non-login: >> scripts

## current shell

### source

* execution in current shell
* source kann auch über einen **.** aufgerufen werden; nicht zu verwechseln mit ./ *filename*, was lediglich eine relative Pfadangabe ist.
* mithilfe von source hat bekommt ein Skript Zugriff auf Daten wie das Arbeitsverzeichnis oder die Umgebungsvariablen der Shell und kann sie verändern; kann verwendet werden um Konfigurationsdateien neu einzulesen ohne das betreffende Programm neu starten zu müssen. 

```sh
source <file> args # liest Kommandos aus *filename* und führt diese in der aktuellen Shell aus; d.h. es wird keine Subshell erzeugt; the execution of *file**name* is done in the invoking context, environment; changes within apply to the current process or the current shell 

. <file> [args]

#! /bin/echo "Please source" 
# when executed by mistake (though I'd expect those files should not be given execution permissions, which would already prevent execution), you get a notice that it should be sourced instead.
```

### exec

```sh
exec <cmd> # with cmd does not create a new process; the ongoing terminal process is replaced by <cmd>
exec # without <cmd>: open, close, or copy file descriptors; kann den stdin/stdout/stderr dauerhaft umlenken (nützlich in Skripts: anstatt hinter jede Befehlszeile einen Redirector zu setzen), 

exec > <file> # lenkt den stdout in file; any output will go to *file*
exec > /dev/tty # lenkt den stdout zurück auf die Bash***
```

### eval

concatenates its arguments into a single string, joining the arguments with spaces, then executes that string as a bash command. It's similar to running bash -c "string", but eval executes the command in the current shell environment rather than creating a child shell process.


# INPUT | OUTPUT | REDIRECTION

## echo

- https://www.gnu.org/software/bash/manual/html_node/Bash-Builtins.html#index-echo
## printf

- https://www.gnu.org/software/bash/manual/html_node/Bash-Builtins.html#index-printf
- https://linuxize.com/post/bash-printf-command/

## redirection

```sh
> # redirects FD1 (stdout)
2> # redirects FD2 (stderr)	
2>&1 # redirects FD2 to FD1
&> # the same?
>> # redirect and append
```

### append

```sh
sudo echo -e "127.0.0.1\talpaca.local" >> /etc/hosts # doesn't work
echo -e "127.0.0.1\talpaca.local" | sudo tee -a /etc/hosts # works
```

## pipes

```sh
cdm1 | cmd2 # connects the stdout of the first process to the stdin of the second
```

Bash will create a subshell for each command and set up the first command's stdout file descriptor such that it points to the second command's stdin file descriptor. The two commands will run simultaneously and bash will wait for both of them to end.

Es können auch komprimierte oder binäre Daten gepiped werden.

### import variables from file

To read variables from a file we can use `source` or `.`
Lets assume the file contains the following line:  
`MYVARIABLE="Any string"`
we can then import this variable using

```shell
#!/bin/bash
source <filename>
echo $MYVARIABLE
```