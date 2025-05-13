# SECURITY

- https://www.linux-magazin.de/ausgaben/2023/03/libre-office-sicherheit/
# CLI

`--convert-to OutputFileExtension[:OutputFilterName][--outdir output_dir][--convert-images-to]` # Batch convert files (implies *--headless*).
If --outdir isn't specified, then current working directory is used as output_dir. If --convert-images-to is given, its parameter is taken as the target filter format for *all* images written to the output format. If --convert-to is used more than once, the last value of OutputFileExtension[:OutputFilterName] is effective. If --outdir is used more than once, only its last value is effective. 
    
example:                                 
`--convert-to pdf *.odt`
`--convert-to epub *.doc`
`--convert-to pdf:writer_pdf_Export --outdir /home/user *.doc`
`--convert-to "html:XHTML Writer File:UTF8"`
`--convert-images-to "jpg" *.doc`  
`--convert-to "txt:Text (encoded):UTF8" *.doc`

# CALC

## time & date

Um Zeitdifferenzen zu berechnen, beispielsweise wie viel Zeit zwischen den Uhrzeiten 23:30  und 01:10 in derselben Nacht liegt, verwenden Sie die folgende Formel:

```sh
=(B2<A2)+B2-A2
```

Dabei steht die  spätere Uhrzeit in B2 und die frühere Uhrzeit in A2. Das Ergebnis des  Beispiels ist 01:40, also 1 Stunde und 40 Minuten.

Die Formel macht sich zunutze, dass ein ganzer Tag mit seinen 24 Stunden den Wert 1 hat und  eine Stunde 1/24 dieses Wertes beträgt. Der logische Wert in den  Klammern ist 0 oder 1, entsprechend 0 oder 24 Stunden. Das Ergebnis der  Formel wird wegen der Reihenfolge der Operanden automatisch im  Zeitformat ausgegeben.

## shortcuts

```sh
Ctrl+Space # selecting a column
Shift+Space # selecting a row
```

multiple columns or rows by first selecting for example two cells in a row (for columns) or column (for rows) and then hitting those shortcuts

### relations

### Absolute Adressierung

Im Gegensatz zur relativen  Adressierung gibt es den absoluten Bezug, der auf folgende Weise  geschrieben wird: $A$1:$B$2. Vor jeder Angabe, die absolut verwendet  werden soll, steht also ein Dollarzeichen.