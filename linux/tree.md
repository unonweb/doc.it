# tree

list contents of directories in a tree-like format

Generate an HTML directory index one level deep, skip, ie `-I`gnore the generated `index.html` file itself, include sizes (`-s`) and timestamps (`-D`):

```bash
tree -H '.' -L 2 --noreport --dirsfirst -T 'Custom Elements'  -s -D --charset utf-8 -P "*.html" -I "index.html" -o index.html

-H <baseHREF> # Turn on HTML output
-L <level> # Max display depth of the directory tree
--dirsfirst # List directories before files.
-s # Print the size of each file in bytes along with the name.
-D # Print the date of the last modification time or if -c is used, the last status change time for the file listed
-I <pattern> # Do not list those files that match the wild-card pattern.  You may have multiple -I options.
-o <filename> # Send output to filename
```

```bash
tree -H '.' \
    -L 1 \
    --noreport \
    --houtro ""
    --dirsfirst \
    --charset utf-8 \
    --ignore-case \
    --timefmt '%d-%b-%Y %H:%M' \
    -I "index.html" \
    -T 'Downloads' \
    -s -D \
    -P "*.zip|*.gz" \
    -o index.html
```

`-H '.'` enable HTML mode and set base href, can be relative e.g. `.` or absolute e.g. `/files`. 
 `-L 1` limit to current directory only 
 `--noreport` do not include the summary at the end 
 `--houtro ""` suppress the credits at the end by setting an empty outro file name 
 `--dirsfirst` put directories first 
 `--charset utf-8` ensure UTF-8 charset 
 `--ignore-case` make the `-I` and `-P` options case insensitive 
 `--timefmt '%d-%b-%Y %H:%M'` set date format (see `man strftime` in your terminal for details [*](https://strftime.org/)). 
 `-I "index.html"` **I**gnore, i.e. do not include the generated `index.html` file in the listing 
 `-P "*.zip|*.gz"` filter by glob **P**attern, e.g. only include zip/gz files 
 `-T` set custom **T**itle (has no effect if `--hintro` (set custom intro file) is defined) 
 `-s` include file **S**izes 
 `-D` include modified **d**ates 
 `-o index.html` write to file (stdout by default)