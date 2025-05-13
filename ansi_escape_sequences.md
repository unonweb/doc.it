# ANSI Sequenzen

* https://www.nayab.dev/linux/misc/escape-codes.html
* https://en.wikipedia.org/wiki/ANSI_escape_code
* https://gist.github.com/JBlond/2fea43a3049b38287e5e9cefc87b2124

`ESC` `[` `{attr1};{attr2};....{attrn)m`

- Beginn der Sequenz: `\033[` bzw. `\e[`
- Dann beliebige Anzahl von Codes getrennt durch eine Semikolon
- Trennzeichen: `;`
- der **letzte** **Code** muss mit `m` abgeschlossen werden:

```sh
\e[ # start a sequence
\e[1m # make it bold
\e[0m # reset

echo -e '\033[1;42mThis is text with green background'
printf '\033[1;42mThis is text with green background'
```

# ESCAPE CHAR

- `\033[` bzw. `\e[`

The ASCII equivalent of `ESC` character is *033(in Octal)* or *0x1b(in Hexadecimal)*. We use 033 because it works for all operating systems.

| Value | Escape Code  | Text Style |
| ----- | ------------ | ---------- |
| 0     | ‘\033[0;31m’ | Regular    |
| 1     | ‘\033[1;31m’ | Bold       |

```sh
\e[0 # alle Attribute und Farben zurücksetzen
\e[1 # fett
\e[2 # kursiv
\e[4 # unterstrichen
\e[5 # blinken
\e[7 # reverse
```

## troubleshooting

In cases **where Bash escape sequences are not supported** (such as `PS3`)  you can get a literal escape character using Bash's printf builtin:

```sh
ESC=$(printf "\e")
PEACH="${ESC}[48;5;209m"
BOLD="${ESC}[1m"
RESET="${ESC}[0m"
```

Every escape sequence starts with a literal escape character, which you can input using the Bash escape sequence `\e`. So for example,`\e[48;5;209m` sets the background to a peachy color (if you have 256 color support) and `\e[2;2H` moves the cursor near the top-left corner of the screen.

In cases where Bash escape sequences are not supported (such as  PS3) you can get a literal escape character using Bash's printf builtin:

# VARIABLES

- https://gist.github.com/JBlond/2fea43a3049b38287e5e9cefc87b2124

```sh
# color
RED="\033[31m"

# text formatting
RESET="\033[0m"
BOLD="\033[1m"
LOW="\033[2m"
ITALIC="\033[3m"
BOLDITALIC="\033[1;3m"
UNDERLINE="\033[4m"
BLINK="\033[5m"
REVERSE="\033[6m"
BACKGROUND="\033[7m"
INVISIBLE="\033[8m"
GREY="\033[38;5;248m"

echo -e "${GREY}grauer Text${RESET}"
```

## with echo

gilt für `echo -e` und $PS1

```sh
echo -e "\e[1mDieser Text ist fett\e[0m"
echo -e "\e[1mDieser Text ost fett \e[4mfett und unterstrichen\e[4m:\e[0m"
```

```sh
$'<ansi>' # wird von BASH ausgewertet
```

## with printf

```sh
printf '\033[1;42mThis is text with green background \n'
printf '\033[31mThis text is red \n'

RED='\033[0;31m'
printf "I ${RED}love Stack Overflow\n"
```