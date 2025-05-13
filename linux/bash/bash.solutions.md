# AT THE TOP

## bash behaviour

```bash
set -e # stop execution on error
set -u # throw error if unknown variable is encountered
set pipefail # catch errors in pipes
```
## variables

```sh
# script location
SCRIPT_PATH="$(readlink -f "${BASH_SOURCE}")"
SCRIPT_DIR=$(dirname -- "$(readlink -f "${BASH_SOURCE}")")
SCRIPT_NAME=$(basename -- "$(readlink -f "${BASH_SOURCE}")")
# formatting
ESC=$(printf "\e")
BOLD="${ESC}[1m"
RESET="${ESC}[0m"
RED="${ESC}[31m"
GREEN="${ESC}[32m"
BLUE="${ESC}[34m"
UNDERLINE="${ESC}[4m"
GREY="${ESC}[37m"
```

# ARGUMENTS & RETURNS

## check: no arguments provided

```sh
# check if there were no arguments provided:
if [ $# -eq 0 ]; then
    >&2 echo "No arguments provided"
    exit 1
fi

# not a dir
if [[ ! -d $1 ]]; then
	echo "$1 is not a directory"
	exit
fi
```

## assign default value

```sh
# assign default value
if [[ -z $1 ]]; then
	echo "assigning default destination: dest='/media/frida/udo_nonner'"
	dest="/media/frida/udo_nonner"
else
    dest=$1    
fi

if [[ -z $2 ]]; then
	echo "assigning default sitesDir=/home/payload/sites"
	sitesDir="/home/payload/sites"
fi
```

## check for existence

```bash
local ignoreComments=false

# Loop through all arguments
for arg in "$@"; do
  if [[ "${arg}" == "--ignore-comments" ]]; then
      echo "readFileToArray(): Ignoring comments"
      ignoreComments=true
      break  # Exit the loop if the argument is found
  fi
done
```

## pass as associative array

```bash
function print_person {
    declare -A person=($@)
    echo "Name: ${person[name]}, Age: ${person[age]}"
}

declare -A alice=( ['name']='Alice' ['age']=30 )
print_person ${alice[@]}
```

## return as nameref

```bash
function createRandomName() {
    local -n _result="$1"
    _result=${RANDOM}
    # printf -v result "%x" $RANDOM
}

function createTempDir() {
    local baseDir="${1}"
    local name
    local path
    
    createRandomName "name"
    path="${baseDir}/${name}"
    mkdir -p "${path}" && echo "Path created: ${path}"
}

createTempDir "/tmp"
```
# ARRAYS

## assign filenames

```sh
files=(*.webc) # assign all .web files in current directory
# quote if the are values separated by space
for file in "${files[@]}"; do
	echo ${file%.webc}.css
	#compNames+=(${file%.webc})
done
```

## printHighestNumFromArr()

```sh
function printHighestNumFromArr() {
  local -n numbers=$1 # must be an array
  local highest
  local number

  # Initialize a variable to hold the highest number
  highest=${numbers[0]}  # Start with the first element

  # Loop through the array
  for number in "${numbers[@]}"; do
    if (( number > highest )); then
      highest=$number  # Update highest if the current number is greater
    fi
  done
  printf '%s' "${highest}"
}
```

# DATE

## printOldestDate()

```bash
function printNameWithOldestDate() {
  local prefixToRemove=$1
  local -n _strings=$2
  # Initialize variables to track the oldest date and corresponding string
  local currentStr=""
  local oldestStr=""
  local oldestDate=""
  local formattedDate

  # Loop through each string
  for str in "${_strings[@]}"; do
    # Extract the date and time part
    currentStr="${str##*$prefixToRemove}"  # Remove the prefix
    # Convert the date and time to a format that can be compared
    # Extract date parts (test-day-month-year-hour:minute)
    IFS='-' read -r day month year time <<< "$currentStr"
    # Convert to sortable format (YYYY-MM-DD HH:MM)
    isoDate="20${year}-${month}-${day} ${time}"
    # Convert to Unix timestamp for comparison
    formattedDate=$(date -d "${isoDate}" +%s 2>/dev/null)

    # Check if this is the first iteration or if the current date is older
    if [[ -z "$oldestDate" || "${formattedDate}" -lt "${oldestDate}" ]]; then
      oldestDate="${formattedDate}"
      oldestStr="${str}"
    fi
  done

  # Output the oldest string
  printf '%s' "${oldestStr}"
}

# use
strings=("test-25-04-14-22:14" "test-25-04-14-22:16" "test-25-04-14-22:10" "test-25-04-14-22:12")
printOldestDate "test-" strings # 25-04-14-22:10
```

```bash
function printNameWithOldestDate() {
  local prefixToRemove=$1
  local -n stringsWithUnixSec=$2
  # Initialize variables to track the oldest date and corresponding string
  local unixSecCurrent=""
  local nameWithOldestDate=""
  local unixSecOldest=""

  # Loop through each string
  for str in "${stringsWithUnixSec[@]}"; do
    unixSecCurrent="${str##*${prefixToRemove}}"  # Remove the prefix
    # Check if this is the first iteration or if the current str is older
    if [[ -z "${unixSecOldest}" || "${unixSecCurrent}" -lt "${unixSecOldest}" ]]; then
      unixSecOldest="${unixSecCurrent}"
      nameWithOldestDate="${str}"
    fi
  done

  # Output the oldest string
  printf '%s' "${nameWithOldestDate}"
}

# use
strings=("test-1744664597" "test-1744664593" "test-1744664897" "test-1744664591")
printNameWithOldestDate "test-" strings # 
```

# DESKTOP

## getActiveDesktopUsers()

```bash
function getActiveDesktopUsers() { # activeDesktopUsers
  # returns an array
  local -n result=${1}
  local userName
  local sessionType
  local active
  local sessionID

  for sessionID in $(loginctl --output=json --no-pager | jq -r '.[].session'); do
    # loop over session IDs
    sessionType=$(loginctl show-session ${sessionID} --value --property=Type) # tty, wayland
    if [[ ${sessionType} != "tty" ]]; then
      # it's a desktop user!
      active=$(loginctl show-session ${sessionID} --value --property=Active)
      if [[ ${active} == "yes" ]]; then
        # user is active!
        userName=$(loginctl show-session ${sessionID} --value --property=Name)
        result+="${userName}"
      fi
    fi
  done
}
```
# ENVIRONMENT
## cmdExists()

```sh
function cmdExists() {
  command -v "$1" &> /dev/null
}
```

## checkDebian()

```sh
function checkDebian() {
    if ! [ -f "/etc/debian_version" ]; then
        echo "Error: This script is only supported on Debian-based systems."
        exit 1
    fi
}
```

## isArrayNotEmpty()

```sh
function isArrayNotEmpty() {
	# use:
	# if isArrayNotEmpty myArray; then
	
	local -n arrayRef=$1  # Use nameref to pass array by reference
	if [[ ${#arrayRef[@]} -gt 0 ]]; then
			return 0  # Array is not empty (success)
	else
			return 1  # Array is empty (failure)
	fi
}
```
## isValueInArray()

```sh
function isValueInArray() {
  # use:
  # isValueInArray "$value" "${array[@]}"
  local element="$1" 
  shift 
	local array=("$@") 
  for item in "${array[@]}"; do 
    if [[ "$item" == "$element" ]]; then 
      return 0 # Found
    fi 
  done 
  return 1 # Not found 
}
```

## only in non-interactive

```bash
if [[ -n "$PS1" ]]; then
    read -p "What terminal type: " termtype
    # ... other code that uses $termtype ...
fi
```
## selectValueFromArray()

```sh
function selectValueFromArray() {

	# USE
	# selectValueFromArray <variable name> <array>
	local -n _value=$1
	local -n _array=$2
	local _choice
	local _index=1
	local _temp

	while true; do
		for item in "${_array[@]}"; do
			echo -e "${GREEN}${_index}${RESET})  ${item}"
			((_index++))
		done
		echo -e "${GREEN}q${RESET})  Quit" # add quit
		read -p ">> " _choice

		if [[ "$_choice" == "q" ]]; then
			# user abort
			return 1
		fi

		_temp=${_array[$((_choice - 1))]} # decrease by 1 because of zero-based array

		if [[ ! -z "$_temp" ]]; then
			# value exists
			_value=$_temp # set value
			unset _temp
			return 0
		else
			# value does not exist
			echo "Invalid choice. Please try again."
			continue
		fi
	done
}
```

## selectValueFromArray()

```bash
function selectValueFromArray() {

	# USE
	# selectValueFromArray <variable name> <array>
	local -n _value=$1
	local -n _array=$2
	local _choice
	local _index=1
	local _temp

	while true; do
		for item in "${_array[@]}"; do
			echo -e "${GREEN}${_index}${RESET})  ${item}"
			((_index++))
		done
		echo -e "${GREEN}q${RESET})  Quit" # add quit
		read -p ">> " _choice

		if [[ "$_choice" == "q" ]]; then
			# user abort
			return 1
		fi

		_temp=${_array[$((_choice - 1))]} # decrease by 1 because of zero-based array

		if [[ ! -z "$_temp" ]]; then
			# value exists
			_value=$_temp # set value
			unset _temp
			return 0
		else
			# value does not exist
			echo "Invalid choice. Please try again."
			continue
		fi
	done
}
```
## remove
### removeEmptyFromArray

```bash
function removeEmptyFromArray() {
    local -n _sparseArray=$1
    local -a _cleanArray=()
    
    for item in "${_sparseArray[@]}"; do
        if [[ -n $item ]]; then 
            _cleanArray+=( "$item" )
        fi
    done

    _sparseArray=("${_cleanArray[@]}")
}

# use:
sparseArray=( "apple" "" "banana" " " "cherry" )
cleanArray=()
removeEmptyFromArray sparseArray
echo "Clean Array: ${cleanArray[@]}"
```

### removeItemsFromArray

```bash
function removeItemsFromArray() {
  # remove items from array
  # items to remove are passed with another array
  local -n _array=$1
  local -n _itemsToRemove=$2
  local _item
  local _remove

  for i in "${!_array[@]}"; do
    _item=${_array[$i]}
    for _remove in "${_itemsToRemove[@]}"; do
      if [[ $_item == *"$_remove"* ]]; then
        unset _array[$i]
      fi
    done
  done
}

# use:
array=( "apple" "" "banana" "coke" "cherry" )
remove=( "coke" )
removeItemsFromArray array remove
echo "${array[@]}"
```

## create/assign

### assign arguments

```sh
# assign a script's input arguments
array=("$@")
```

### from newline delimited list

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

### from string

```sh
# array from string
string="root:x:0:0:root:/root:/bin/bash"
array=(${string//:/ })
echo ${array[0]} # => root
```
### from cmd output

```sh
# assign command output to array
mapfile array < <(find . -maxdepth 1 -type d) # read array from find output
```

#### pngs

```sh
# assign with loop
declare -a pngs
for png in $(ls -f *.png); do
	echo "found $png"
	pngs+=($png)
done
echo ${pngs[@]}
```

#### rsync

```bash
CMD="rsync --archive --progress --dry-run ${RSYNC_SRC}/ ${RSYNC_DST}"
mapfile rsyncResult < <($CMD) # read array from find output
for line in "${rsyncResult[@]}"; do
  echo -n "$line"
done
```

#### lvm snap

```sh
mapfile < <(sudo lvs -o lv_full_name | grep snap)
echo ${MAPFILE[@]}
```

### assign filenames

```sh
files=(./*.webc) # assign all .web files in current directory
```

### assign file content

The easiest and safest way to read a file into a bash array is to use the **mapfile** builtin which read lines from the standard input. When no array variable name is provided to the **mapfile** command, the input will be stored into the **$MAPFILE** variable. Note that the **mapfile** command will split by default on newlines character but will preserve it in the array values, you can remove the trailing delimiter using the **-t** option and change the delimiter using the **-d** option.

```bash
mapfile MYFILE < example.txt
printf '%s' "${MYFILE[@]}"
#line 1
#line 2
#line 3

mapfile < example.txt # Use default MAPFILE array
printf '%s' "${MAPFILE[@]}"
#line 1
#line 2
#line 3
```

```sh
# assign file content to array
mapfile array <file # reads file line by line adding values to the array
IFS=$'\n' read -r -a array < file
```

# ERRORS

## handleError()

```sh
function handleError() {
  local exit_code=$1
  local error_message="$2"
  log "Error: $error_message (Exit Code: $exit_code)" "error"
  exit $exit_code
}
```

## subshells

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

# FILESYSTEM

## sourceConfigFile()

- accept relative and absolute paths
- set default if no path is provided
- accept both styles:
    - `--config-file=<value>` 
    - `--config-file <value>`

```bash
function sourceConfigFile() {
  # requires all cli arguments to be passed as $@
  # does not work if positional and named args are mixed
  # works if only named args are used!

  local _args=("$@")
  local _arg
  local _scriptDir=$(dirname -- "$(readlink -f "${BASH_SOURCE}")")
  local _scriptName=$(basename -- "$(readlink -f "${BASH_SOURCE}")")
  local _defaultFile="${_scriptDir}/${_scriptName/.sh/}.cfg" # fk.report-space.sh.cfg
  local _configFile

  # Loop through args
  for i in "${!_args[@]}"; do
    case ${_args[i]} in
    --config-file|--config)
      _configFile="${_args[i + 1]}"
      ;;
    --config-file=*|--config=*)
      _configFile="${_args[i]#*=}" # extract value after '='
      ;;
    esac
  done

  _configFile=${_configFile:-"$_defaultFile"} # set default
  _configFile=$(realpath $_configFile)

  if [[ -r $_configFile ]]; then
    echo "Sourcing config from $_configFile"
    source $_configFile
  else
    echo "ERROR: Path not readable: $_configFile"
    exit 1
  fi
}
```

## readFileToArray()

```bash
function readFileToArray() {
    local -n result=${1} # array
    local file=${2}
    local lines=()
    local lines
    local ignoreComments=true
    local feedback=${CONFIG[feedback]}
    local funcName=${FUNCNAME[0]}

    if [[ ${feedback} == true ]]; then
      echo "${GREY}--- ${funcName}()${RESET}"
    fi

    # Check if the file exists
    if [[ ! -f "${file}" ]]; then
        echo "Error: File not found: ${file}"
        return 1
    fi

    # Read the file line by line and append to the array
    while IFS= read -r line; do
      if [[ ${ignoreComments} == true ]] && [[ "${line}" == \#* ]]; then
        continue
      else
        lines+=("${line}")  # Append the line to the array
      fi
    done < "${file}"

    if [[ ${#lines[@]} -gt 0 ]]; then
        if [[ ${feedback} == true ]]; then
          echo "Found ${#lines[@]} lines"
        fi
        result=("${lines[@]}") # assign
        return 0
    else
        echo "Error: File empty? ${file}"
        return 1
    fi
}
```

## readFileToMap()

```bash
function readFileToMap() { # MAP ${pathFile} 
  local -n _result=${1} # map
  local filePath=${2}

  while IFS='=' read -r key value; do
    # ignore if comment
    if [[ "${key}" == \#* ]]; then
      continue
    fi
    # trim whitespace from key and value
    key="${key#"${key%%[![:space:]]*}"}"  # Trim leading whitespace
    key="${key%"${key##*[![:space:]]}"}"  # Trim trailing whitespace
    value="${value#"${value%%[![:space:]]*}"}"  # Trim leading whitespace
    value="${value%"${value##*[![:space:]]}"}"  # Trim trailing whitespace
    # add to result
    _result["$key"]="${value}"
  done < ${filePath}

  if [[ ${#_result[@]} -eq 0 ]]; then
    echo "ERROR: Nothing read from file ${GREEN}${filePath}${RESET}"
    return 1
  else
    return 0
  fi
}
```

## list paths of directory

```sh
# list paths of directory
dir=/home/payload/sites
for entry in "$dir"/*; do
  echo "$entry"
done
```

## list filenames of directory

```sh
# list filenames of directory
# -mindepth 1 in order to exclude the current directory
dir=/home/payload/sites
find $dir -maxdepth 1 -mindepth 1 -type d -exec basename {} \;
# assign 
mapfile dirnames < <(find $dir -maxdepth 1 -mindepth 1 -type d -exec basename {} \;)
echo ${dirnames[@]}
```

## getDirsInPath()

```bash
function getDirsInPath() {
  # <dirs> <path>
  local -n result=${1}
  local -n path=${2}

  mapfile result < <(find ${path} -maxdepth 1 -mindepth 1 -type d -exec basename {} \;)
  echo "${#result[@]} directories found"
}
```
## isDir()

```sh
isDir() {
	if [ -d /path/to/directory ]; then
  		echo "Directory exists."
	else
  		echo "Directory doesn't exist."
	fi
}
```

## mkdir if not exist

```bash
# Create mount point if it doesn't exist
if [ ! -d "${_mountPoint}" ]; then
  echo "Creating mountpoint: ${GREEN}${_mountPoint}${RESET}"
  mkdir -p "${_mountPoint}"
fi
```
## assign file content to variable

```bash
FILENAME=.ssh/authorized_keys
FILE_CONTENT=$(<"$FILENAME")
echo $FILE_CONTENT
```

## is a mountpoint

```bash
if ! mountpoint -q /media/common; then 
    echo "not a mountpoint"
else 
    echo "already a mountpoint"
fi
```

# LOG & REPORT

## log()

```sh
function log() {
  local message="$1"
  local type="$2"
  local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
  local color
  local endcolor="\033[0m"

  case "$type" in
    "info") color="\033[38;5;79m" ;;
    "success") color="\033[1;32m" ;;
    "error") color="\033[1;31m" ;;
    *) color="\033[1;34m" ;;
  esac

  echo -e "${color}${timestamp} - ${message}${endcolor}"
}
```

## info()

```sh
function info() {
    printf "\n%s %s\n\n" "$( date )" "$*" >&2; 
	# produces a message like
	# Mon Oct  7 21:45:27 UTC 2024 Pruning repository
}
```

## sendReport()

```bash
function sendReport() {
  local scriptName=${SCRIPT_NAME}
  local reportStatus=${REPORT_STATUS}
  local report=${REPORT}
  local mailFrom=${MAIL_FROM}
  local mailTo=${MAIL_TO}
  local subject="[${scriptName}] Report ${reportStatus} for $(hostname)"
  
  echo -e ${report} | mail -r "${mailFrom}" -s "${subject}" "${mailTo}" &
}
```

## addToReport()

```bash
function addToReport() {
  # add line to report
  local -n report=REPORT
  
  report="${1}\n"
  echo "${1}"
}
```
# INTERACTION
## read

### getDirection()

```sh
function getDirection() {
	
	local dir

	echo "Which direction? "
	echo -e "\t 1) to";
	echo -e "\t 2) from"; 
	
	read -n 1 -p ">> " dir
	echo ""
	
	case "$dir" in
		1)
			getDirection_return="to" && return 0;;
		2)
			getDirection_return="from" && return 0;;
		*)
 			echo "Error: wrong input: $dir" && return 1;;
	esac
}
```

### proceedWithEnter()

```sh
function proceedWithEnter() {
	input=
	while true; do
		echo "Hit enter to proceed!"
		read -n 1 -p ">> " -r input
		echo ""
		if [[ -z $input ]]; then
			break
		fi
	done

	echo "Proceed!"	
}
```

### proceedOrAbort()

```sh
function proceedOrAbort() {
	input=
	while true; do
		echo -e "\e[1mHit 'enter' to start or ctrl + c to abort\e[0m"
		echo "Execute the following command?"
		echo $CMD
		read -n 1 -p ">> " -r input
		echo ""
		if [[ -z $input ]]; then
			break
		fi
	done
}
```

## hit y to start

```sh
input=
while [[ $input != "y" ]]; do
  echo "start backup from '$src' to '$dest'"?
	echo -e "** ${b_}hit y to start${_reset}**"
	read -n 1 -p ">>" input
done
```

## select
### basic select

```bash
PS3="Your choice: "
select DOMAIN in "${DOMAINS[@]}"; do
    if [[ $DOMAIN ]]; then
        echo "You entered '$REPLY' and selected '$DOMAIN'."
        break
    else
        echo "Error: Invalid choice '$REPLY'"
    fi
done
```

## select with case

```bash
PS3="Select the operation: "

select opt in add subtract multiply divide quit; do

  case $opt in
    add)
      read -p "Enter the first number: " n1
      read -p "Enter the second number: " n2
      echo "$n1 + $n2 = $(($n1+$n2))"
      ;;
    subtract)
      read -p "Enter the first number: " n1
      read -p "Enter the second number: " n2
      echo "$n1 - $n2 = $(($n1-$n2))"
      ;;
    multiply)
      read -p "Enter the first number: " n1
      read -p "Enter the second number: " n2
      echo "$n1 * $n2 = $(($n1*$n2))"
      ;;
    divide)
      read -p "Enter the first number: " n1
      read -p "Enter the second number: " n2
      echo "$n1 / $n2 = $(($n1/$n2))"
      ;;
    quit)
      break
      ;;
    *) 
      echo "Invalid option $REPLY"
      ;;
  esac
done
```
### selectMachine()

```sh
function selectMachine() {
	
	echo -en "${RED}"
	options=(
		"fk-mobil21"
	)
	PS3="${RESET}${BOLD}With which machine do you want to rsync? >> ${RESET}"
	select option in "${options[@]}"; do
		case $option in
			"fk-mobil21")
				machine=fk-mobil21
				localPath="/home/frida/franzk"
				remotePath="udo@fk-mobil21:/home/udo/it"
				break
				;;
			*)
				echo "Invalid option. Try again."
				;;
		esac
	done
}
```

# MISC

## count lines

* count_lines.sh
* outputs the total number of lines that exist in whatever file the user enters
* interactive version

```sh
#!/bin/bash

echo -n "Please enter a filename: "
read filename
nlines=$(wc -l < $filename)

echo "There are $nlines lines in $filename"
```

* version that accepts one cmd line argument; run with `./count_lines.sh /etc/passwd`

```sh
#!/bin/bash

nlines=$(wc -l < $1)
echo "There are $nlines lines in $1"
```

* version that accepts multiple cmd line arguments:

```sh
#!/bin/bash

n1=$(wc -l < $1)
n2=$(wc -l < $2)
n3=$(wc -l < $3)

echo "There are $n1 lines in $1"
echo "There are $n2 lines in $2"
echo "There are $n3 lines in $3"
```

# NET

## isValidIP() 

```bash
function isValidIP() {
    local ip="${1}"
    # Use a regular expression to validate the IP address
    if [[ "${ip}" =~ ^([0-9]{1,3}\.){3}[0-9]{1,3}$ ]]; then
        # Split the IP into its components
        IFS='.' read -r i1 i2 i3 i4 <<< "${ip}"
        # Check if each component is between 0 and 255
        if (( i1 >= 0 && i1 <= 255 )) && (( i2 >= 0 && i2 <= 255 )) && (( i3 >= 0 && i3 <= 255 )) && (( i4 >= 0 && i4 <= 255 )); then
            echo "Valid IP address"
            return 0
        fi
    fi
    echo "Invalid IP address"
    return 1
}
```

```bash
function isValidIP() {
    local ip=${1}
    local octet
    local octets
    
    # Regex to match a valid IPv4 address
    if [[ ${ip} =~ ^([0-9]{1,3}\.){3}[0-9]{1,3}$ ]]; then
        # Check that each octet is between 0 and 255
        IFS='.' read -r -a octets <<< "${ip}"
        for octet in "${octets[@]}"; do
            if [ "${octet}" -lt 0 ] || [ "${octet}" -gt 255 ]; then
                return 1  # Not a valid IP address
            fi
        done
        return 0  # Valid IP address
    else
        return 1  # Not a valid IP address
    fi
}
```

## isPrivateIP()

```bash
function isPrivateIP() { # ${ip}
	local ip=${1}

	[[ -z "${ip}" ]] && return 1
	[[ ${ip} =~ ^(10\.|192\.168\.|172\.(1[6-9]|2[0-9]|3[0-1])\.|169\.254\.) ]] && return 0
	return 1
}
```

## isHostReachable()

```bash
function isHostReachable() {
  local host=${1}
  
  # Ping the host
  if ping -c 1 "${host}" &> /dev/null; then
      return 0
  else
      return 1
  fi
}
```
# NUMBERS

## next

```bash
number=5
next=${number}; (( next++ ))
echo $next # 6
```
# PROCESSES

## kill zombie

```sh
function clrz() {
    # Clear zombie processes
    ps -eal | awk '{ if ($2 == "Z") {print $4}}' | kill -9
}
```

## killit()
```sh
# Kills any process that matches a regexp passed to it
function killit() {
  ps aux | grep -v "grep" | grep "$@" | awk '{print $2}' | xargs sudo kill
}
```

# STRINGS

## transform

### trim()

```sh
function trim() {
    local str="$1"

    # remove leading whitespace characters
    str="${str#"${str%%[![:space:]]*}"}"
    # remove trailing whitespace characters
    str="${str%"${str##*[![:space:]]}"}"
    
    printf '%s' "$str"
}

str="  hello! "
str=$(trim $str)
echo $str # hello!
```

## test

### startsWith

```bash
[[ $a == z* ]]   # True if $a starts with an "z" (pattern matching).
```
### string in list

```bash
if ! [[ "$cms" =~ ^(wordpress|meganto|typo3)$ ]]; then get_cms ; fi
```

```bash
if [ "$cms" != wordpress -a "$cms" != meganto -a "$cms" != typo3 ]; then
```

```bash
if [ "$cms" != wordpress ] && [ "$cms" != meganto ] && [ "$cms" != typo3 ]; then
```

```sh
if [[! "$dir" =~ ^(to|from)$ ]]; then
    echo "Error: wrong input: $dir"
    return 1;
else
    return $dir
fi
```

### case

```bash
case "$cms" in
  wordpress|meganto|typo3)
    do_your_else_case
    ;;
  *)
    do_your_then_case
    ;;
esac
```

