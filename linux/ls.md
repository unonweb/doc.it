# ls

```sh
ls -l 
# -rw-r--r--. 1 root root  4017 Feb 24  2022 vimrc
```

-  `-` # file type (no special type)
-  `rw-r--r--` # permissions
   - `rw-` # owner
   - `r--` # group
   - `r--` # others
-  Extended attributes: dot (`.`)
-  User owner: `root`
-  Group owner: `root`

```sh
ll # ist ein Alias von 
ls -alF
ls -a
ls --all # zeigt auch versteckte Dateien
ls -l # long listing format
ls -F
```

```sh
ls -i
ls --inode # 

ls --classify # append indicator (one of */=>@|)
```

### size

```sh
ls -s
ls --size # print the allocated size of each file, in blocks

# A file's size and the amount of disk space it takes up may differ. Consider for example, if you open new file, seek 1G into it (i.e. jump at that point without writing anythin), and write something, the OS doesn't allocate 1G (plus the space for something) on disk, it allocates only the same for something -- this is called a "**sparse file**".

ls -s # lists the amount of storage space used by the file's content (excluding space used for metadata). 
```

### sort

```sh
# sort by time
ls -c # sort by change timestamp, newest first (last time the metadata of a file was changed, e.g. file permission changes)
ls -t # sort by modification timestamp, newest first (last time the contents of a file were modified)
ls -u # sort by access timestamp, newest first (the last time a file was read)
```

```sh
# sort by size
ls -S # sort by file size, largest first
ll -Sh # print size and summary, sort everything by size
ls -X # sort alphabetically by entry extension
```

### file types

```sh
#### [Dateitypen]

c =** Character Device; **l** = link; **-** = Datei; **b** = block device; **s** = socket; **p** = pipe
```

### format

```sh
ls -l # long listing format; doesn't display size correctly?
ls --full-time # like *-l --time-style=full-iso*
ls -n # nummerische Anzeige von Benutzern
ls -h
ls --human-readable
```

### layout

```sh
ls # list as columns
ls -1 # list one file per line
ls --width 1 # the same
ls -m # comma separated list of entries
```

### directories

```sh
ls -R
ls --recursive # list subdirectories recursively
ls -d
ls --directory # list directories themselves, not their contents

\# useful with pattern matching

ls -ld *[0-9] # lists matching filenames only in the current directory
ls -l *[0-9] # lists matching filename recursively
```
