
# VARIABLES

## assign

```sh
# assign string to variable
domain='haerer'
echo $domain

# [assign command output to variable]
variable=$(command)
size=$(identify -format "%wx%h" schwäbische_alb_karte.png)
pathGlobalAssets=$(realpath "../_global_assets/")

# [assign file content to variable]
variable=$(<config.txt)
```

### local

Local variables are declared inside a block of code or a function.  The scope of these variables is visible only within the block where they are declared. A local variable is declared inside a function and is only visible within that function.

```bash
local variablename=variablevalue
```

## access

```sh
domain=hello
echo ${domain}.com
```

## global environment variables

```sh
$PATH
$HOME
$SHELL # The name of the shell that will launch when you open a terminal window. On most Linux distributions, this will be bash unless you changed it from the default.
$TERM # Terminal windows are actually emulations of a hardware terminal. This holds the type of hardware terminal that will be emulated.
$USER # The username of the current person using the system.
$PWD # The path to the current working directory.
$OLDPWD # The directory you were in prior to moving to the current working directory.
$LS_COLORS # The list of color codes used by the ls highlight different file types.
$MAIL # If the mail system has been set up on your Linux computer (by default, it isn't), this will hold the path to the current user's mailbox.
$PATH # A list of directories that the shell will search through to find command executables.
$LANG # The language, localization, and character encoding settings.
$HOME # The home directory of the current user.
$_ # The underscore (_) environment variable holds the last command that was typed.
```

To create your own global environment variables, add them to the `/etc/environment` file. You'll need to use `sudo` to edit this file:

```sh
sudo nano /etc/environment
```

### path

Don't add export commands to `~/.bashrc` because only interactive Bash shells read this configuration file. 
If we open a non-interactive shell or a shell other than Bash, our PATH change won’t be reflected.

```bash
# ~/.profile
export PATH=/some/new/path:$PATH # prepend to PATH
export PATH=$PATH:/some/new/path # append to PATH 
```

## assign

```sh
# set for a single cmd:
VAR1=VALUE1 VAR2=VALUE2 ... Command ARG1 ARG2...
# set for the current session
export VAR1=VALUE1
export VAR2=VALUE2
...
Command ARG1 ARG2...
# or more compact:
export VAR1=VALUE1 VAR2=VALUE2; Command ARG1 ARG2...
```

add new env variables that are always available:

```sh
# ~/.bashrc
export INHERITED_VAR="available to all child processes"
```

## declare

- https://www.gnu.org/software/bash/manual/html_node/Bash-Builtins.html#index-declare
- **declare restricts the scope of a variable**

```bash
declare -p # lists the existing environment variables; produces an output with environment variables using the declare -x syntax which allows for easy export of an environment to a file and can be easily sourced later on.
```

### scope

```bash
declare -g <name> # forces <name> to be created or modified at the global scope, even when declare is executed in a shell function
```
### attributes

```bash
declare -r <name> # Make names readonly
declare -t <name> # Give each name the trace attribute. Traced functions inherit the DEBUG and RETURN traps from the calling shell. The trace attribute has no special meaning for variables.

```
### type

```bash
declare -a <name> # declare an indexed array variable
declare -A <name> # declare an associative array variable
declare -i <name> # The variable is treated as an integer; arithmetic evaluation is performed when the variable is assigned a value.
declare -n <name> # Give each name the nameref attribute, making it a name reference to another variable. That other variable is defined by the value of name. All references, assignments, and  attribute modifications to name, except those using or changing the -n attribute itself, are performed on the variable referenced by name's value.  The nameref attribute cannot be applied to array variables.
```
### export

```sh
declare -x <name># Mark names for export to subsequent commands via the environment.
declare -x <KEY>=<VALUE> # adds an environment variable to the exported environment
declare +x <KEY> # removes an environment variable from the exported environment
```
### uppercase/lowercase

```bash
declare -u <name> # When the variable is assigned a value, all lower-case characters are converted to upper-case. The lower-case attribute is disabled.
declare -l <name> # When the variable is assigned a value, all upper-case characters are converted to lower-case. The upper-case attribute is disabled.
```
## export

```sh
export <KEY>=<VALUE> # adds an environment variable to the exported environment 
export -n <KEY> # removes an environment variable from the exported environment
export [-p] # lists the existing environment variables; produces an output with environment variables using the declare -x syntax as described above.
```

## unset

```sh
unset <KEY> # Solely used to remove (unset) a shell variable or function. If the variable is an environment variable, it will also be removed from the list of exported variables.
```

## printenv

```sh
 printenv # exclusively print out the names and values of the environment variables, with one key/value pair per line.
```

## set

* despite its name, cannot be used to set/declare an environment variable
* can only print existing variables or set and unset shell specific options or shell positional parameters.

```sh
set # lists all existing shell variables (local and environment variables) when no argument is provided
set -k # exports all shell local variables to the environment. This is there for a legacy reason and should be avoided. Exporting only the necessary environment variables individually with declare or export should be preferred.
```

## env

* Can be used to alter the environment variable before executing a command or print the environment
* The changes to the environment are not persisted in the current shell environment.

```sh
env <KEY>=<VALUE> <cmd> # adds an environment variable to the exported environment
env -u KEY <cmd> # removes an environment variable from the exported environment
env -u TZ -u HOME MY_ENV=prod HOME=/opt/app myApp # The syntax can combine adding or removing variables
```

# BASH VARIABLES

### script location

#### BASH_SOURCE

* The most robust way to get a script’s location is the BASH_SOURCE special variable
* It’s roughly equivalent to $0, but with predictable results.

```sh
# script file
filename=$(basename $BASH_SOURCE)
filename=${BASH_SOURCE:-$(basename $0)} # either uses BASH_SOURCE or $0

# Script location variables
scriptPath="$(readlink -f "${BASH_SOURCE}")"
scriptDir=$(dirname -- "$(readlink -f "${BASH_SOURCE}")")
scriptName=$(basename -- "$(readlink -f "${BASH_SOURCE}")")
echo "working directory is $(pwd)"
echo "scriptPath = $scriptPath"
echo "scriptDir = $scriptDir"
echo "scriptName = $scriptName"

echo "$filename" # init-site.sh
```

#### $0

```sh
$0 # Expands to the name of the shell or shell script. This is set at shell initialization. If Bash is invoked with a file of commands (see Shell Scripts), $0 is set to the name of that file. If Bash is started with the -c option (see Invoking Bash), then $0 is set to the first argument after the string to be executed, if one is present. Otherwise, it is set to the filename used to invoke Bash, as given by argument zero. 

# script path
$0 # /home/username/Desktop/scripts/test.sh

# script dir
src_dir=$(dirname "$0") # /home/username/Desktop/scripts/
```

## special parameters

- https://www.gnu.org/software/bash/manual/bash.html#Special-Parameters-1

## environment variables

```sh
$BASHOPTS # The command-line options that were used when bash was launched.
$BASH_VERSION # The bash version number as a string of words and numbers.
$BASH_VERSINFO # The bash version as a digit.
$COLUMNS # The current width of the terminal window.
$DIRSTACK # The directories that have been added to the directory stack by the pushd command.
$HISTFILESIZE # Maximum number of lines permitted in the history file.
$HISTSIZE # Number of lines of history allowed in memory.
$HOSTNAME # The hostname of the computer.
$IFS # The Internal Field Separator used to separate input on the command line. By default, this is a space.
$PS1 # The PS1 environment variable holds the definition for the primary, default, and command prompt. A set of tokens called escape sequences can be included in the definition of your command prompt. They represent such things as the host- and username, the current working directory, and the time.
$PS2 # When a command spans more than one line and more input is expected, the secondary command prompt is shown. The PS2 environment variable holds the definition of this secondary prompt, which, by default, is the greater than sign (>).
$SHELLOPTS # Shell options you can set using the set option.
$UID # The User Identifier of the current user.
```

```sh
($!) # Expands to the process ID of the job most recently placed into the background, whether executed as an asynchronous command or using the bg builtin (see Job Control Builtins).
```

### IFS

Set IFS (Internal Field Separator). Shell uses the IFS variable to determine what the field separators are. By default, IFS is set to the space character. Change it to the newline character, as demonstrated below:

```sh
#!/bin/bash
names="Netgear
Hon Hai Precision Ind. Co.
Apple"
    
SAVEIFS=$IFS   # Save current IFS (Internal Field Separator)
IFS=$'\n'      # Change IFS to newline char
names=($names) # split the `names` string into an array by the same name
IFS=$SAVEIFS   # Restore original IFS

for (( i=0; i<${#names[@]}; i++ ))
do
    echo "$i: ${names[$i]}"
done
```

### parameters

```sh
$# # 1; expands to the number of positional parameters
$* # --> "$1c$2c" where c is the first character of the value of the IFS variable. 
# If IFS is unset, the parameters are separated by spaces. 
# If IFS is null, the parameters are joined without intervening separators. 
$@ # --> "$1" "$2" ...
```

# EXPANSION
## arithmetic expansion

```sh
((expr))
(( expr ))

((var--)) # decreases var by 1
((var++)) # increases var by 1
```

## brace expansion

- can only be used to generate lists of words; cannot be used for *pattern matching*
- while *globs* only expand to actual filenames, brace *expansion expand* to any possible permutation of their contents
- *brace expansion* happens before *filename expansion*

```sh
echo a{d,c,b}e # ade ace abe
echo {1..5} # 1 2 3 4 5 
echo {5..1} # 5 4 3 2 1
echo {0,1}{0..5} # 01 02 03 04 05 10 11 12 13 14 15
```

## parameter expansion

### `#var[@]`

```sh
names=(frida ursula stanislaw)
for (( i=0; i<${#names[@]}; i++ )); do
    echo "$i: ${names[$i]}"
done
```

### default values

It’s important to account for the fact that a variable may have one of several states:

- assigned value
- unset
- null string

Thus, to avoid unexpected errors, we can use the ${param:-param} expression along with similar expressions to assign a default value to a variable under specific conditions.

- `:-` Default if unset or empty) → More common & preferred.  
- `:=` (Default only if unset)

```sh
FOO="${x:-default_value}" # If variable not set or null, use default_value. 
# Important: No assignment to the x variable takes place, and the variable retains its original value.

VARIABLE="${1:-$DEFAULTVALUE}" # assigns to VARIABLE the value of the 1st argument passed to the script or the value of DEFAULTVALUE if no such argument was passed. Quoting prevents globbing and word splitting.

# The colon builtin (:) ensures the variable result is not executed
# The double quotes (") prevent globbing and word splitting.
```

```sh
FOO="${VARIABLE:=default}"  # If variable not set or null, set it to default.
```

```sh
timeSpan="${timeSpan:-$TIME_SPAN}"
# works if timeSpan is unset or empty, assigning TIME_SPAN as the default.
```
### remove from end

```sh
${var%%pattern} # remove the longest match from the end
band_name=${song_file%%-*} # Nina Simone - 1967 - Silk & Soul => Nina Simone

${var%pattern} # remove the shortest match from the end
${var%"${var##*[![:space:]]}"} # removes trailing whitespaces

${var%/} # remove trailing slash if present
```

### remove from beginning

```sh
${var#pattern} # remove the shortest match from the beginning: var is expanded, and the shortest string that matches pattern is removed from the beginning of the expanded value
${var#"${var%%[![:space:]]*}"} # remove leading whitespaces if present
${var#/} # remove leading slash if present
${var##pattern} # remove the longest match from the beginning
scriptname=${0##*/} # /home/frida/scripts/test => test
```

### replace pattern

```sh
${var//pattern} # removes pattern 
${var//pattern/} # replaces pattern with empty string (the same?)

${var/pattern/string} # replace first match by string: var is expanded, and the first match of pattern is replaced by string
${var//pattern/string} # replace all matches by string: every match of pattern is replaced
```

### prefix/suffix

```sh
${var/#/string} # add prefix
${var/#/string} # add suffix
```

If multiple parameter expansion have to be executed on a parameter/variable, multiple statements are needed:

```sh
file=$HOME/image.jpg
file=${file##*/} # => image
```