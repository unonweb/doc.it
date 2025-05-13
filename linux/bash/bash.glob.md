
- **glob** is the name of the type of pattern supported by bash
- bash uses globbing for *filename* *expansion*
- bash expandiert/vervollständigt die Globs und übergibt sie dem aufgerufenen Programm.
- globs will never jump into subdirectories; they only match against file names in their own directory

```sh
ls ~/*/hello.txt # here bash will search through all directories in our home directory for the given file
```

- if "quoted" globs will turn literal caracters

## test

```sh
[[ string = pattern ]]    # is true if string matches globs pattern
[[ string != pattern ]]   # is true if string doesn't match globs pattern

[[ "GNU/Linux is an operating system" == *"Linux"* ]] && echo 'yes' || echo 'no'

[[ $a == z* ]]   # True if $a starts with an "z" (pattern matching).
[[ $a == "z*" ]] # True if $a is equal to z* (literal matching).
```
## special caracters

`*` #  matches **any string of any given length**; any kind of text, even no text at all; schließt keine versteckten Dateien mit ein
`?` # matches any one single character

```sh
ls test-?.txt # all .txt files named ‘test-‘ followed by a single char
ls ????.txt # all .txt files with a name of exactly four characters
```

```sh
[char]
[0-9]
[a-z] # matches a single character, if it's in the given set
*[a-z] # lowercase characters
[A-Z] # uppercase characters
[a-zA-Z] # alphabet
[a-zA-Z0-9] # alphanumeric

[!char] # matches a single caracter, if it's not in the given set

[[:char_class:]] # matches all characters belongig to a POSIX character class
[![:char_class:]] # matches all characters that don't belong to *char_class*

[[:lower:]] # lowercase characters
[[:upper:]] # uppercase characters
[[:alpha:]] # all alphabets
[[:alnum:]] # all alphabets and digits
[[:word:]] # word characters (letters, numbers and underscores) `[A-Za-z0-9_]`
```
## examples

```sh
[A-Z]*.[0-9][0-9][0-9]_[!A] # Spricht Jede Datei an, deren Name mit einem Großbuchstaben ([A-Z]) beginnt. Danach darf irgendwas folgen(*), beliebig lang. Dann muß ein Punkt kommen, gefolgt von drei Ziffern ([0-9]). Dem folgt ein Unterstrich und dann ein beliebiges Zeichen nur eben kein A.
```

## extended globs

```sh
shopt -s extglob # enables extended globs
# --> it's necessary to include this line at the top of scripts using the extglob feature
```

```sh
+(pattern|pattern) # matches **one or more** **of** the given patterns

*(pattern|pattern) # matches **zero or more** **of** the given patterns

?(pattern|pattern) # matches **zero or one** **of** the given patterns

@(pattern|pattern) # matches **one of** the given patterns (**logical OR operator**)

!(pattern|pattern) # **none of** …; matches anything but the given pattern
```

```sh
echo !(*.jpg|*.bmp) # all files except .jpg and .bmp
echo !(*([r-t]))acy # anything that does not start with zero or more occurrences of the letters r, s and t
echo un-!(*haerer*).css # all .css files that do not contain "haerer"
echo un-!(*--*).css # all .css files that do not contain "--" 
```

