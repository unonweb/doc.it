# read

- https://linuxcommand.org/lc3_man_pages/readh.html
- https://www.gnu.org/software/bash/manual/html_node/Bash-Builtins.html#index-read

Takes the user input and splits the string into fields, assigning each new word to an argument. 

If there are fewer variables than words, **`read`** stores the remaining terms into the final variable.

liest den stdin und schreibt den Wert in eine oder mehrere Variablen

```sh
read <var> # liest die komplette Eingabe bis ENTER und schreibt sie in *var*
read var1 var2 var3 # die Eingabe wird anhand des Leerzeichens in einzelne Elemente getrennt und in die versch. Variablen geschrieben; wenn mehr Parameter übergeben werden als Variablen angegeben sind, übernimmt die letzte Variable sämtliche verbleibenden Parameter.
```
