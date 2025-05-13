# READ
## REPLY

The command stores a user's input into the **`$REPLY`** variable by default.

```sh
# assign to specified variable
read input
echo $input

# assign to default variable REPLY
read
echo $REPLY
```
## options

```sh
read <options> <arguments>

read -a <array>	# Assigns the provided word sequence to a variable named <array>.
read -d <delimiter>	# Reads a line until the provided <delimiter> instead of a new line.
read -e	# Starts an interactive shell session to obtain the line to read.
read -i <prefix>	# Adds initial text before reading a line as a prefix.
read -n <number> # Returns after reading the specified number of characters while honoring the delimiter to terminate early
read -N <number> # Returns after reading the specified number of chars, ignoring the delimiter.
read -r	# Disable backslashes to escape characters.
read -s # silent; Eingabe am Bildschirm nicht sichbar
read -t <time>	# The command times out after the specified time in seconds.
read -u <file descriptor>	# Read from file descriptor instead of standard input.
```

```bash
read -p <prompt> # output <prompt> without a trailing newline before attempting to read; if the prompt is to be customized, 'quoted' ANSI sequences have to be used:
read -p $'\e[1mtext\e[0m: '
```
## examples

```sh
echo 'a b c d e' | while read var1 var2 var3; do
	echo $var3 $var2 $var1
done
# prints 'c d e b a'
# Dies ein Verhalten des Lesebefehls ist, dasselbe geschieht nicht mit den Parametern eines Skripts.
```

```sh
# infinite loop-read-break]
while true; do 
	read -p "Please type 3 numbers >" N1 N2 N3 && break # breaks the loop if it was successful
	echo "Did not understand your results , please try again"
done
```

```sh
# read user input as an array
while read line; do # used to read data from stdin line by line
	echo $line
done
read -ep ">> " -a array
```

### count lines in filename

```sh
#!/bin/bash

echo -n "Please enter a filename: "
read filename
nlines=$(wc -l < $filename)

echo "There are $nlines lines in $filename"
```

# select

The `[LIST]` can be a series of strings separated by spaces, a range of numbers, output of a command, an array, and so on.

* Als Prompt wird hier immer der Inhalt der Variablen PS3 angezeigt, der bei den Shells meistens mit #? vorbelegt ist.
* Bei select handelt es sich somit um eine **Endlosschleife**, die Sie nur mit der Tastenkombination `strg+d` (EOF) oder den Befehlen `exit` bzw. `break` innerhalb von `do` und `done` beenden können

```sh
select ITEM in [LIST]; do
  [COMMANDS]
done
```

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

```sh
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

# comparison
## with select

```bash
function useSelect() {

  select path in "${PATHS_LOCAL[@]}"; do
    if [[ -n "$path" ]]; then
      LOCAL_PATH=$path
      break
    else
      echo "Invalid choice. Please try again."
    fi
  done
}
```

## with read

* functionally the same as above
* more flexible
* more complex

```bash
function setLocalPath() {

	local _answer
	local _localPath
	local _index=1

	while true; do
		for path in "${PATHS_LOCAL[@]}"; do
			echo -e "${GREEN}${_index}${RESET})  ${path}"
			((_index++))
		done
		echo -e "${GREEN}q${RESET})  Quit" # add quit
		read -p ">> " _answer

		if [[ "$_answer" == "q" ]]; then
			return 1
		fi
        # decrease by 1 because of zero-based array:
		_localPath=${PATHS_LOCAL[$((_answer - 1))]} 

		if [[ ! -z "$_localPath" ]]; then
			LOCAL_PATH=$_localPath
			return 0
		else
			echo "Invalid choice. Please try again."
			continue
		fi
	done
}
```