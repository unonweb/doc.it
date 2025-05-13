# echo

```sh
echo -n # do not output the trailing newline
echo -e # enable interpretation of backslash escapes
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
echo -e "\e[1mDieser Text ost fett \e[4mfett und unterstrichen\e[4m:\e[0m"
```

```sh
\e[0 # alle Attribute und Farben zurücksetzen
\e[1 # fett
\e[2 # kursiv
\e[4 # unterstrichen
\e[5 # blinken
\e[7 # reverse

$'<ansi>' # wird von BASH ausgewertet
```
