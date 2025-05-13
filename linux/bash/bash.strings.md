
# COMPARE

```sh
[[ string1 = "string2" ]]   # is equal
[[ string1 != "string2" ]]  # is not equal
[[ string1 < string2 ]]     # true if the first string sorts before the second
[[ string1 > string2 ]]     # true if the first string sorts after the second
```

## note

always use:


```sh
[[ "$src_ip" == "212.227.171.69" ]] # 
```

instead of:

```sh
[ $src_ip = "212.227.171.69" ] # Problem: If src_ip is empty or undefined, the condition will throw an error (unary operator expected).
```
# EXTRACT

```sh
#!/bin/bash
function useBashExpansion() {
    line="$1"
    
    # Extract everything after SRC= and take first word
    temp=${line#*SRC=}; srcIP=${temp%% *}
    
    # Extract everything after DST= and take first word
    temp=${line#*DST=}; destIP=${temp%% *}
    
    # Extract everything after SPT= and take first word
    temp=${line#*SPT=}; srcPort=${temp%% *}
    
    # Extract everything after DPT= and take first word
    temp=${line#*DPT=}; destPort=${temp%% *}
}
```

```bash
#!/bin/bash
function useBashRematch() {
  line="$1"
  [[ $line =~ SRC=([^ ]+) ]] && _srcIP=${BASH_REMATCH[1]} # set _srcIP
  [[ $line =~ DPT=([^ ]+) ]] && _destPort=${BASH_REMATCH[1]} # set _destPort
}
```

# ASSIGN

## assign file content to variable

```bash
FILENAME=.ssh/authorized_keys
FILE_CONTENT=$(<"$FILENAME")
echo $FILE_CONTENT
```
# REGEX

```bash
[[ $line =~ SRC=([^ ]+) ]] && _srcIP=${BASH_REMATCH[1]} # set _srcIP
```

- `()`
  capture group
- `[^ ]`
  matches any character except a space
- `+` 
  means "one or more" of those characters
# TEST

## compare

```sh
[[ string1 = "string2" ]]   # is equal
[[ string1 != "string2" ]]  # is not equal
[[ string1 < string2 ]]     # true if the first string sorts before the second
[[ string1 > string2 ]]     # true if the first string sorts after the second
```

## substring

* only `[[`
* globs: pattern matching with *globs* is applied automatically whenever `pattern` *is not quoted*:

### regex

When `=~` is used, the right string is considered as a regular expression.

- Advanced search criteria
- Don‘t need exact matching
- Anchor patterns to string start/end
- Perform case-insensitive searches

```sh
[[ string =~ pattern ]] # true if string matches regex pattern; do not quote! 
# best practice is to store the regular expression in a variable and test by:
[[ string =~ $regex ]]
```

```bash
string='Hello world!';

if [[ $string =~ "world" ]]; then
  echo "Found 'world' in string."
fi
```

- The period followed by an asterisk `.*` matches zero or more occurrences any character except a newline character.

```sh
#!/bin/bash

STR='GNU/Linux is an operating system'
SUB='Linux'

if [[ "$STR" =~ .*"$SUB".* ]]; then
  echo "It's there."
fi
```

```sh
if [[ $string =~ ^$substring ]]; then 
  # find the substring at the start of the string
fi
```

#### BASH_REMATCH

`$BASH_REMATCH` is a special array variable in the Bash shell that stores the results of matching a regular expression using the `=~` operator within a script.

When you use the `=~` operator to match a regular expression against a string, any captured groups (i.e., parenthesized subexpressions) are stored in the `${BASH_REMATCH[@]}` array starting from index 1. The entire matched text is stored at index 0.

```bash
string="Hello World"  
pattern='(H[a-z]+)\s*(W[a-z]+)'  
if [[ $string =~ $pattern ]]; then  
    echo "Match found!"  
    for i in "${!BASH_REMATCH[@]}"; do  
        echo "$i: ${BASH_REMATCH[$i]}"
    done  
fi

# Output:
# 0: Hello World  
# 1: Hello  
# 2: World
```
### glob

```sh
[[ string = pattern ]]    # is true if string matches globs pattern
[[ string != pattern ]]   # is true if string doesn't match globs pattern

[[ "GNU/Linux is an operating system" == *"Linux"* ]] && echo 'yes' || echo 'no'

[[ $a == z* ]]   # True if $a starts with an "z" (pattern matching).
[[ $a == "z*" ]] # True if $a is equal to z* (literal matching).
```

```bash
string='Hello world!'

if [[ $string == *"world"* ]]; then
  echo "Found 'world' in string."
fi
```

```sh
#!/bin/bash

STR='GNU/Linux is an operating system'
SUB='Linux'
if [[ "$STR" == *"$SUB"* ]]; then
  echo "It's there."
fi
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

# HERE DOCUMENTS

```bash
# create report

REPORT=
REPORT+="REPORT\n"
REPORT+="------\n"
REPORT+="HOST: $(hostname)\n"
REPORT+="DATE: $(date)\n"
REPORT+="THRESHOLD: $THRESHOLD\n"
REPORT+="\n"
REPORT+="ALERTS\n"
REPORT+="------\n"
for item in "${ALERTS[@]}"; do
  REPORT+="$item\n"
done
echo -e $REPORT | mail -s "$SUBJECT" "$EMAIL"
```