# less

### shortcuts

`home` # Jump to the top of the file

`end` # Jump to the end of the file

type line number and hit `g` # jump to a specific line

### open a file with a search term

You can use the `-p` (pattern) option to cause `less` to search through the text file and find the first matching item. It  will then display the page with the matching search item in it, instead  of the first page of the file. Unless, of course, the search item is  found on the first page of the file.

**There is no space between the `-p` and the search term**         

```sh
iw list | less -pCapabilities
```

### filter and search inside less

`&<pattern>` Display only lines which match the pattern. If pattern is empty (if you type & immediately followed by ENTER), any filtering is turned off, and all lines are displayed.

```sh
&dns # will display only lines that match the pattern dns
&!dns # will filter out (exclude) those lines, displaying only lines that don't match the pattern
&arp.*eth0 # will display lines containing arp followed by eth0
&arp|dns # will display lines containing arp or dns
```

```sh
h # display help
r # repaint screen
```

