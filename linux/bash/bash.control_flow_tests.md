# LOOPS

## keywords

### break

- used to exit from a `for`, `while`, `until` , or `select` loops

### continue

`continue` statement skips the remaining commands inside the body of the enclosing loop for the current iteration and passes program control to the next iteration of the loop.

## while
## for 

### C-style loop

`for (( variable assignment; condition; iteration process )); do commands ; done`

* the assignment of the variable inside C-style for loop can contain spaces unlike the usual assignment
* variables inside C-style for loop aren't preceded with `$`

```shell
for (( i = 0; i < 10; i++ )); do
  echo "The iteration number is $i"
done	
```

```sh
declare -a names=(frida ursula stanislaw)

for (( i=0; i<${#names[@]}; i++ )); do
    echo "$i: ${names[$i]}"
done
```

### iterate over array keys

```sh
for key in "${!fruits[@]}"
do
  echo "Key for fruits array is: $key"
done
```

### iterate over array values

```sh
for item in ${array[@]}; do
	echo "$item"
done

# quote if the are values separated by space!
for item in "${commands[@]}"; do
	echo $item
done
```

```sh
for item in ${path_extensions[@]}; do
	if [ -d $item ]; then
		export PATH="$item:$PATH"
	else
		echo -e "error: ${cyan_}$item${_reset} doesn't exist"
	fi
done
```

## troubleshooting

### loops and subshells

Take this code:

```sh
sudo journalctl --grep "UFW ALLOW" --since ${TIME_SPAN} --no-pager | while read line; do
  LINES+=($line)
done
```

The **Problem:** The `while read line; do` loop is running inside a pipeline. This means the loop runs in a **subshell**, and **any modifications to variables inside the loop do not persist outside the loop**.

**Fix:** Instead of using a pipeline, use **process substitution**:

```sh
while read line; do
    ...
done < <(sudo journalctl --identifier=kernel --grep "UFW ALLOW" --since ${TIME_SPAN} --no-pager)
```
### find & loops

* Using `for` with `find` is the wrong approach here, see for example this [writeup](http://mywiki.wooledge.org/BashPitfalls#for_i_in_.24.28ls_.2A.mp3.29) about the can of worms you are opening.
* The recommended approach is to use `find`, `while` and `read` as described [here](http://mywiki.wooledge.org/BashFAQ/001).

```bash
# This way you delimit the filenames with null (`\0`) characters
# This means that variation in space and other special characters will not cause problems.

find . -type f -name '*.*' -print0 | 
while IFS= read -r -d '' file; do
    printf '%s\n' "$file"
done
```

# trap

`trap` built-in is used to _trap_ (catch) any supported signals and react upon it.
The list of supported signals can be checked by running the `trap -l` command.

When the `trap` built-in is used, it will listen for signals that it supports. If the `trap` built-in is told to check for a signal, when that signal is received by the script, `trap` will execute the action you mentioned.

```bash
trap ACTIONS SIGNALS
```

```bash
#!/usr/bin/env bash

function sigInt() {
    echo "The sleep was disturbed by Jarasandha"
}

trap sigInt SIGINT

sleep 10m
```

```bash
# send a signal
kill -s SIGNAL PID
kill -s SIGINT 28551
```
# if

```bash
if [ 0 -eq 1 ]; then
  echo '0=1'
elif [ 0 -eq 2 ]; then
  echo '0=2'
else
  echo '0!=2'
fi
```

# logic & order

* only [[

```sh
( expr )       	# returns the value of expr; mit Klammer kann die Rangordnung bestimmt werden
! expr         	# true if expr is false
[[ ! -z $var ]] # if var is not empty

[[ expr1 && expr2 ]] # or
[[ expr1 ]] && [[ expr2 ]] # true if both expr1 and expr2 are true

[[ expr1 || expr2 ]] # or
[[ expr1 ]] || [[ expr2 ]] # true if either expr1 or expr2 is true
```

# TESTS

## `[[ ]]`  vs `[ ]`

* the brackets `[[ ... ]]` must be separated from all other arguments with whitespace
* all arguments withing `[[ … ]]` must be separated by a space (including operators)

## "unary operator expected"

```sh
[ $src_ip = "212.227.171.69" ] # Problem: If src_ip is empty or undefined, the condition will throw an error (unary operator expected).
```

**Fix:** Use `[[ ... ]]` for safe comparisons:

```sh
[[ "$src_ip" == "212.227.171.69" ]] # 
```

## strings
### case

```bash
case "${1}" in 
 1) echo "Option was 1!";;
 2) echo "Option was 2!";;
 *) echo "Option was something else: '${1}'";;
esac
```

## booleans
### true | false

```sh
# true is shell command and always return 0 
# false always return 1 
failed=true
if $failed; then 
    echo "Job failed"
else 
   echo "Job done"
fi
```

## integers
### ((integer))

```bash
INTERACTIVE=0
if ((INTERACTIVE)); then
  echo "TRUE"
else
    echo "FALSE"
fi

if (( ! INTERACTIVE)); then
  echo "TRUE"
else
    echo "FALSE"
fi
```

### comparison

`<` and `>` inside `[[ ... ]]` compare strings, not numbers!

```sh
[[ val1 -eq val2 ]] or val1 = val2        # is equal
[[ val1 -ne val2 ]] or val1 != val2       # not equal
[[ val1 -gt val2 ]] or ((val1 > val2))    # greater than
[[ val1 -ge val2 ]] or ((val1 >= val2))   # greater or equal
[[ val1 -lt val2 ]] or ((val1 < val2))    # less than
[[ val1 -le val2 ]] or ((val1 <= val2))   # less or equal
```
## is (not) empty

```sh
# true if empty (length = zero):
[[ -z string ]]
[[ -z $var ]]

# true if not empty (length != zero):
[[ -n string ]] or [[ string ]]
[[ -n $var ]] or [[ $var ]]
```

```sh
if [[ -z "" ]]; then echo "empty"; else echo "not empty"; fi
```

```shell
if [ -z ${var} ]; then 
	echo "var is unset"; 
else 
	echo "var is set to '$var'"; 
fi
```

## files

### access

```sh
[[ -e filename ]]   # exists
[[ -r filename ]]   # exists + readable
[[ -w file ]]       # exists + writable
[[ -x file ]]       # exists + executable
[[ -s filename ]]   # exists + not empty
[[ -d file ]]       # exists + directory

[[ -b file ]] # exists + block device
[[ -c file ]] # exists + character device
[[ -g file ]] # exists + SGID-Bit gesetzt
[[ -u file ]] # exists + SUID-Bit gesetzt
[[ -k file ]] # exists + Sticky-Bit gesetzt
[[ -f file ]] # exists + regular file
[[ -L file ]] # exists + symbolic link
[[ -O file ]] # owned by you
[[ -G file ]] # owned by your group
```

```sh
if [ -d /path/to/directory ]; then
  echo "Directory exists."
else
  echo "Directory doesn't exist."
fi
```

### comparison

```sh
[[ file1 -nt file2 ]]   # file1 is newer than file2
[[ file1 -ot file2 ]]   # file1 is older than file2
```