
`grep [options] "pattern" [file]`  

# SOLUTIONS

```sh
journalctl -N | grep ^_ # get all lines starting with _
```

```bash
# get all users who are allowed to log in:
sudo grep --invert-match --regexp "/sbin/nologin" --regexp "/usr/bin/nologin" --regex "/bin/false" /etc/passwd
# additionally extract user field:
sudo grep --invert-match --regexp "/sbin/nologin" --regexp "/usr/bin/nologin" --regex "/bin/false" /etc/passwd | awk -F':' '{print $1}' 
```
## display all output but highlight search matches

```sh
grep -z <pattern>
```

```sh
egrep "pattern|$"
```

here `|` is treated as regex OR condition; so pattern and all end of lines are catched 
(which is every line, i.e. everything is catched but since '$' is a hidden character, it cannot be highlighted

```sh
grep "pattern\|$" # the same
```

```sh
grep -E "pattern|$" # the same
```

## search filenames

```sh
grep -rni "<pattern>" * 
# = recursive i.e, search subdirectories within the current directory
# n = to print the line numbers to stdout
# i = case insensitive search
```

## search for text in files

```sh
grep bash /etc/passwd # single file
grep "admin" jslog.txt apachelog.txt # multiple files
```

### grep files in directory

```sh
grep -rni 'hosts' /home/frida/doc/it/linux/ # search for 'host' in all files within that path
```

```sh
grep --recursive --line-number --ignore-case --context 5 'hosts' /home/frida/doc/it/linux/ # search for 'host' in all files within that path
# recursive
# line-number
# ignore-case
```

```sh
grep -r "error" . # grep within current directory
```

### grep files with specific extension

```sh
grep -r "admin" ./site --include \*.js # will only Grep .js files within the ‘site’ directory
```

# PATTERN SYNTAX

```sh
grep -F # or
fgrep # fast grep; ignoriert regex 

fgrep "$," <textfile> # durchsucht textfile nach dem Zeichen $ (statt nach dem Zeilendezeichen)
grep -E <pattern> # or
egrep <pattern> # interpret <pattern> as an extended regular expression
```

## regex

* https://linuxize.com/post/regular-expressions-in-grep/

```sh
grep 'kan..roo' file.txt # begins with “kan”, has two characters, ends with “roo”
grep 'acce[np]t' file.txt # matches lines that contain “accept” or “accent”

grep 'co[^l]a' file.txt # matches strings 
# starting with “co” 
# followed by any letter except “l” 
# followed by “a” 
# such as “coca”, “cobalt” and so on, but will not match the lines containing “cola”

grep '^[A-Z]' file.txt # matches lines that start with a capital letter

grep -E '^[A-Z].*[.,]$' file.txt # matches all lines that starts with capital letter and ends with either period or comma
```

### character classes

| `[:alnum:]` | Alphanumeric characters. |
| ----------- | ------------------------ |
| `[:alpha:]` | Alphabetic characters.   |
| `[:blank:]` | Space and tab.           |
| `[:digit:]` | Digits.                  |
| `[:lower:]` | Lowercase letters.       |
| `[:upper:]` | Uppercase letters.       |

### quantifiers 

Quantifiers allow you to specify the number of occurrences of items that must be present for a match to occur. 
The following table shows the quantifiers  supported by GNU `grep`:

| Quantifier | Description                                     |
| ---------- | ----------------------------------------------- |
| `*`        | Match the preceding item zero or more times.    |
| `?`        | Match the preceding item zero or one time.      |
| `+`        | Match the preceding item one or more times.     |
| `{n}`      | Match the preceding item exactly `n` times.     |
| `{n,}`     | Match the preceding item at least `n` times.    |
| `{,m}`     | Match the preceding item at most `m` times.     |
| `{n,m}`    | Match the preceding item from `n` to `m` times. |

# FILE/DIRECTORY SELECTION

```sh
grep -r # or
rgrep # recursive grep; rekursive Suche in Unterverzeichnissen
grep -a # or
grep --text # Process a binary file as if it were text
```

# MATCHING CTRL

```sh
grep -i
grep --ignore-case # ignoriert die Groß-/Kleinschreibung
grep -v # or 
grep --invert-match # select non-matching lines
```

# OUTPUT CTRL

```sh
grep -q
grep --quiet # do not write anything to stdout

grep -q <goodword> <file> # exit status 0 if file contain *goodword*

! grep -q <badword> <file> # exit status 0 if file does not contain *badword* (! negates the commands exit status)

grep -o 
grep --only-matching <pattern> # gibt nicht die ganze Zeile, sondern nur die passenden Textstellen

grep -n
grep --line-number <pattern> # gibt die Zeilennr. vor jedem Treffer aus

grep -c
grep --count <pattern> # gibt die Anzahl der Zeilen an, auf die das Muster passt
grep --color <pattern> # 
```

# CONTEXT LINE

```sh
grep -C
grep --context <n> <pattern> # Print n lines of output context
```
