
* vor `{` steht mindestens ein Leerzeichen oder ein Newline-Zeichen
* vor `}` steht ein Semikolon oder ein Newline-Zeichen
* Jedes Kommando wird mit einem Semikolon oder einem Newline-Zeichen beendet!
* zwischen `()` stehen keine Argumente!
* The `function` keyword can be omitted if `()` are present
* Alternatively, we can omit the `()` if we use the `function` keyword.
* The function **definition must be placed before any calls to the function**.

# SYNTAX

```sh
function myFunction { 
	command1; 
	command2; 
} [ redirection ]
```

```sh  
myFunction() {
  commands
}
```

`hello () { echo "hello"; }` # Semikolon!  

`hello` # hello

# ARGUMENTS

```sh
function say() { 
    echo "$1"
}
say hello # hello
```

## argument variables

```sh
${#} # expands to the number of positional parameters

${*} # expands to the positional parameters, starting from one
echo "${*}" # returns a single string with arguments separated by the first character of $IFS (by default a blank space)
echo ${@} # returns a separate string for each argument preserving field separation
```

## default values

It’s important to account for the fact that a variable may have one of several states:

- assigned value
- unset
- null string

Thus, to avoid unexpected errors, we can use the ${param:-param} expression along with similar expressions to assign a default value to a variable under specific conditions.

- `:-` Default if unset or empty) → More common & preferred.  
- `:=` (Default only if unset)

```sh
SEND_MAIL=${1:-"true"}
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

## namerefs

- **pass-by-reference**

By default, Bash variables are global unless declared as `local` within a function. However, when you pass variables as arguments to a function, they are accessed via positional parameters like `$1`, `$2`, etc., and any changes to these parameters inside the function do not affect the original variables outside the function.

Namerefs allow you to essentially **define a pointer to another variable**. By creating a nameref, you can indirectly reference and manipulate the target variable without knowing its name beforehand. This is incredibly useful for writing generic functions that can operate on different variables based on input parameters.

* **The nameref attribute cannot be applied to array variables**

```sh
#!/usr/bin/env bash

# Declare a variable
original_var="Hello, World!"

# Function that creates a nameref to a variable
function create_ref() {
    declare -n ref="$1"
    ref="Hello from nameref!"
}

# Call the function with the name of the variable
create_ref original_var

# Print the updated variable
echo "$original_var"
```

By running the `create_ref` function, we can dynamically update the value of `$original_var`, which exists outside of it. Notice that the function doesn’t even need to know about `$original_var`; it works on any variable name provided, making it generic.

## pass associative array

```bash
function foo {
    local -n data_ref=$1
    echo ${data_ref[a]} ${data_ref[b]}
}

declare -A data
data[a]="Fred Flintstone"
data[b]="Barney Rubble"
foo data
```
## pass array

- https://stackoverflow.com/a/73293067

As the local variables get assigned, I had to `shift` the positional parameters away, in order to end with the `( "$@" )` assigning what's left to the indexed questions array.

The indexed array is needed so that we can reliably iterate over all the questions, either in random or ordered sequence. Associative arrays are not ordered data structures, so are not meant for any sort of predictable iteration.

```bash
function serve_quiz_question() {
    local index="$1"; shift
    local -n answers_ref=$1; shift
    local questions=( "$@" )

    current_question="${questions[$index]}"
    echo "current_question: $current_question"
    #...
    current_answer="${answers_ref[$current_question]}"
    echo "current_answer: $current_answer"
}

declare -A answers 
answers[braveheart]="scotland"
answers[mr robot]="new york"
answers[tron]="vancouver"
answers[devs]="california"

# integers would actually be assigned to index \
# by iterating over a random sequence, not shown here.
index=2
declare -a questions=( "braveheart" "devs" "mr robot" "tron"  )

serve_quiz_question "$index" answers "${questions[@]}" 
```



## parsing

### getops

* supports *short* command-line options only
* returns a question mark "`?`" for an unidentified option

```sh
getops # reads all options specified at the command line and creates a list of those options

getopts <options> <var> [arg ...]
# <options> is a list of the valid option letters
# arg is the optional list of parameters to be processed.

while getopts 'abc' OPTION; do 
	# illegal options create error messages
done

while getopts ':abc' OPTION; do 
	# no error messages are created
done 
```

```sh
#!/bin/bash

while getopts ':abc' OPTION; do
	case "$OPTION" in
	a)
		echo "Option a used"
		;;
	b)
		echo "Option b used"
		;;
	c)
		echo "Option c used"
		;;
	?)
		echo "Usage: $(basename $0) [-a] [-b] [-c]"
		exit 1
		;;
	esac
done
```



```sh
while getopts ":h" option; do
   case $option in
      h) # display Help
         Help
         exit;;
   esac
done

# Notice the double semicolon at the end of the exit statement in the case option for -h. This is required for each option. Add to this case statement to delineate the end of each option.
```

### getopt

* GNU’s getopt command
* *getopt* and *getopts* are two different utilities
* parse long cli-options

#### usage

```sh
getopt --options <shortopts> --longoptions <longopts> --name "$0" -- "$@"
getopt --name <progname> # The name that will be used by the getopt routines when it reports errors
getopt -- # the parameters to parse come after the first occurrence of '--'

OPTS=$(getopt --options <shortopts> --longoptions <longopts> --name "$0" -- "$@")

eval set -- "$OPTS" # is typically used to take a string of command-line options stored in the variable OPTS and set the positional parameters of the script accordingly. This is a common technique when you want to pass a set of options to a script as a single variable.

# Each parameter not starting with a '-', and not a required argument of a previous option, is a non-option parameter.
```

#### short options

- Each single character stands for an option.
- A single colon (`:`) means that the option has a required argument or value.
- A double colon (`::`) means that the option has an optional argument or value.

```sh
getopt -o
getopt --options <shortopts> # The short (one-character) options to be recognized. If this option is not found, the first parameter of getopt that does not start with a '-' (and is not an option argument) is used as the short options string. Each short option character in shortopts may be followed by one colon to indicate it has a required argument, and by two colons to indicate it has an optional argument.
           
# Example: We want our script to accept the following options:
# -v
# -f <file>

# Our option string would be:
getopt --options vf:
```

#### long options

1. Options are separated by , (*comma* character).
2. A : (*colon* character) tells that the option has a required argument
3. A :: (two consequent *colon* character) tells that the option has an optional argument.

```sh
getopt -l
getopt --longoptions <longopts> # The long options to be recognized. More than one option name may be specified at once, by separating the names with commas. This option may be given more than once, the longopts are cumulative. Each long option name in longopts may be followed by one colon to indicate it has a required argument, and by two colons to indicate it has an optional argument.
getopt --longoptions file: # required argument
getopt --longoptions file:: # optional argument

# Example: We want our script to accept the following options:
# --verbose
# --file <file>
getopt --longoptions verbose,file:
```

#### example: chatGTP

```sh
#!/bin/bash

# Set default values for options
VERBOSE=false
FILE=""
POSITIONAL=()

# Define the usage function
usage() {
  echo "Usage: $0 [-v] -f <file> positional_arg1 [positional_arg2 ...]"
  exit 1
}

# Parse command-line options
options=$(getopt -o vf: --long verbose,file: -n "$0" -- "$@")
if [ $? -ne 0 ]; then
  usage
fi

eval set -- "$options"

# Process options
while true; do
  case "$1" in
    -v | --verbose)
      VERBOSE=true
      shift ;;
    -f | --file)
      FILE="$2"
      shift 2 ;;
    --)
      shift
      break ;;
    *)
      POSITIONAL+=("$1")
      shift ;;
  esac
done

# Check for required positional arguments
if [ "${#POSITIONAL[@]}" -eq 0 ]; then
  echo "Error: At least one positional argument is required."
  usage
fi

# Additional script logic using the parsed options and positional arguments
if [ "$VERBOSE" = true ]; then
  echo "Verbose mode enabled"
fi

if [ -n "$FILE" ]; then
  echo "File specified: $FILE"
fi

echo "Positional arguments: ${POSITIONAL[@]}"

# Additional script logic...
```

#### example: medium

medium.com

```bash
#!/bin/bash
# Parse command-line options
# Option strings
SHORT=vf:
LONG=verbose,file:

# read the options
OPTS=$(getopt --options $SHORT --long $LONG --name "$0" -- "$@")
if [ $? != 0 ]; 
	then echo "Failed to parse options...exiting." >&2 ; exit 1 ;
fi

eval set -- "$OPTS"

# set initial values
VERBOSE=false

# extract options and their arguments into variables.
while true ; do
  case "$1" in
    -v | --verbose )
      VERBOSE=true
      shift
      ;;
    -f | --file )
      FILE="$2"
      shift 2
      ;;
    -- )
      shift
      break
      ;;
    *)
      echo "Internal error!"
      exit 1
      ;;
  esac
done

# Print the variables
echo "VERBOSE = $VERBOSE"
echo "FILE = $FILE"
```

#### example baeldung

* baeldung.com

```bash
#!/bin/bash

VALID_ARGS=$(getopt -o abg:d: --long alpha,beta,gamma:,delta: -- "$@")
if [[ $? -ne 0 ]]; then
    exit 1;
fi

eval set -- "$VALID_ARGS"
while [ : ]; do
  case "$1" in
    -a | --alpha)
        echo "Processing 'alpha' option"
        shift
        ;;
    -b | --beta)
        echo "Processing 'beta' option"
        shift
        ;;
    -g | --gamma)
        echo "Processing 'gamma' option. Input argument is '$2'"
        shift 2
        ;;
    -d | --delta)
        echo "Processing 'delta' option. Input argument is '$2'"
        shift 2
        ;;
    --) shift; 
        break 
        ;;
  esac
done
```

- *-o* option represents the short command-line options
- *–long* option represents the long command-line options

#### example: dustymabe

```sh
#!/bin/bash

# Call getopt to validate the provided input. 
options=$(getopt -o brg --long color: -- "$@")
[ $? -eq 0 ] || { 
    echo "Incorrect options provided"
    exit 1
}
eval set -- "$options"
while true; do
    case "$1" in
    -b)
        COLOR=BLUE
        ;;
    -r)
        COLOR=RED
        ;;
    -g)
        COLOR=GREEN
        ;;
    --color)
        shift; # The arg is next in position args
        COLOR=$1
        [[ ! $COLOR =~ BLUE|RED|GREEN ]] && {
            echo "Incorrect options provided"
            exit 1
        }
        ;;
    --)
        shift
        break
        ;;
    esac
    shift
done

echo "Color is $COLOR"
exit 0;
```

### shift

Mithilfe von shift kann eine unbestimmte Anzahl von Parametern bearbeitet werden, obwohl in einer Schleife immer nur der erste Parameter verarbeitet wird. Die Schleife wird solange wiederholt, bis keine Parameter mehr übrig sind.

… Parameter3 wird zum Parameter2; Parameter2 wird zum Parameter1; der erste Parameter fällt weg.

### parsing

```sh
# multiple paramenter parsing using a while loop

while [[ $# > 0 ]]; do
	case "$1" in
		-f|--file)
			file="$2"; shift # $2 >> $1; $1 wird verworfen
			;;
		-d|--delimiter)
			delimiter="$2"; shift # $2 >> $1; $1 wird verworfen
			;;
		--help|*)
			echo "Usage:"
			echo "--valueA \"value\""
			echo "--valueB \"value\""
			echo "--help"
			exit 1
			;;
	esac
	shift # if -a or -b were chosen, one shift already happened, which means that $3 is now on $1 
done
```

# RETURN

* other programming languages: concept of a return value that is used to send data back to the original calling location.
* Bash: set a *return status*. Similar to how a program or command exits with an exit status which indicates whether it succeeded or not. We use the keyword `return` to indicate a return status.

```sh
function someFctn() {
	return 0
}
```

return stops the execution of a Bash function

If we supply an integer argument to *return*, it returns that argument to the function caller as the exit status. 
Otherwise, it returns the exit status of the last command executed before *return* to the function caller

### return as exit status 

* only integer

```bash
#!/bin/bash

# Function to multiply two numbers and return the result as an integer
function multiply_numbers() {
  local num1=$1
  local num2=$2
  local multiplication=$((num1 * num2))
  # Returning the multiplication as the exit status
  return $multiplication
}
# Call the function and capture the return value
multiply_numbers 5 3
result=$?
echo "Multiplication result: $result"
```

#### return boolean

```bash
#!/bin/bash

function file_exists() {
  file_path="$1"
  [[ -f "$file_path" ]] && return 0  # Return 0 for true (file exists)
  return 1  # Return 1 for false (file doesn't exist)
}

if file_exists "file.txt"; then
  echo "File exists!"
else
  echo "File not found."
fi
```

### return with echo

The `$(...)` operator doesn't capture a command's "return value", it captures that command's *output*, i.e. text written to stdout. You can use it here, but the function needs to `echo` its result, not "return" it.

```bash
function getSomeString {
   echo "tadaa"
}

VARIABLE=$(getSomeString)
```

#### return multiple values

```bash
#!/bin/bash

function multiple_values() {
  # Create an array and assign values to it
  local array=("Ubuntu" "Linux" "Red-hat")
  # Return the array
  echo "${array[@]}"
}

# Call the function and store the result in an array
result=($(multiple_values))
# Access the individual values from the array
echo "First value: ${result[0]}"
echo "Second value: ${result[1]}"
echo "Third value: ${result[2]}"
```

### use global variables

```shell
#!/bin/bash

function add_numbers {
  local num1=$1  # First argument
  local num2=$2  # Second argument

  # Assign the sum to a global variable
  result=$((${num1} + ${num2}))
}

# Call the function with two numbers as arguments
add_numbers 5 7

# Access the global variable
echo "The sum is: ${result}"  # Outputs: The sum is: 12
```

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

getDirection
echo $getDirection_return
```

# EXIT STATUS

* convention: `0` = success; any value between 0 and 255 = failure
* whenever a command ends it notifies its parent (shell/calling script) of its exit status
* `$?` holds the exit status of the last executed command
* `!` negates the commands exit status:
* `grep -q goodword <file>` # exit status 0 if file contain goodword
* `! grep -q badword <file>` # exit status 0 if file does not contain badword
* the exit status is carried through from whichever command was most recently executed; skipping a command doesn't change it; that's why it's importan to use {} to group commands:
  * `rm file || { echo 'Could not delete file!' >%2; exit 1; }`
