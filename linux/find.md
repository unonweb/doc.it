
```sh
find [options] [starting-point...] [expression]
```

* if no starting-point is specified `.` is assumed.

# EXAMPLES / TIPPS

```sh
# counts all files beneath path:
find /any/path -type f | wc -l

# gets all current subdirectories:
find . -maxdepth 1 -type d

# read array from find output:
mapfile array < <(find . -maxdepth 1 -type d) 

# get absolute paths:
find ~+ -cmin -60 # tilde expansion

# filenames:
find . -type d -exec basename {} \;
```

```sh
# find all files from user daemon that have been modified in the last 24h:
find / -user daemon -mtime -1 -type f
```
## change permissions

In most cases, the files and directories should not have the same permissions. Generally the files do not require the execute permission, whereas you must set execute permissions on the directories to be able to `cd` into them.

The most common scenario is to recursively change the website file’s permissions to `644` and directory’s permissions to `755`.

```sh
find /var/www/html -type d -exec chmod u=rwx,go=rx {} \;
find /var/www/html -type f -exec chmod u=rw,go=r {} \;

find /var/www/html -type d -print0 | xargs -0 chmod 755 
find /var/www/html -type f -print0 | xargs -0 chmod 644
```
# HARDLINKS

```sh
find <start> -xdev -samefile <file> -printf '%n %p\n'
# gibt sowohl die Anzahl als auch die Orte der Hardlinks einer DATEI aus. Ist die ausgegebene Hardlinks-Anzahl größer als die ausgegebenen Orte, so existieren weitere Hardlinks außerhalb vom STARTPUNKT

# find all hardlinks belonging to a file
find ~/code/web/projects/sites/11ty/ -samefile ./layout_page_global.11ty.cjs
```

# EXCLUDE

```sh
### inverse grep
find <path> -iname <pattern> | grep -v <directory-to-ignore> # ignore the location by using inverse grep “grep -v” option

### ! -path
find <path> -iname <pattern> ! -path directory-to-ignore/* # ...but the man page says: "To ignore a whole directory tree, use `-prune` rather than checking every file in the tree."

### prune
find <path> -path <directory-to-ignore/*> -prune -o -iname <findme> -print
```

# NAME

```sh
find -name <pattern> #  Base of file name (the path with the leading directories removed) matches shell pattern pattern.
find -iname <pattern> # Like -name, but the match is case insensitive
find . -name "un-deploy-site.sh"

find 2024-01 -name *.webp -o -name *.JPG # multiple conditions
```

# USER / GROUP

```sh
find <path> -user <user> # zeigt nur Dateien, die username gehören.  
find <path> -group <group> # File belongs to group gname (numeric group ID allowed).  
find <path> -nouser # no user corresponds to file's numeric user ID  
find <path> nogroup #
```

```sh
sudo find / -type f -not -path "/home/*" -not -path "/proc/*" -user "karl" 
# find all files belongig to karl outside of */home/karl* and */proc*

find /home/frida -type f -not -user frida 
# find all files in frida's home directory NOT belonging to user 'frida'

find /home/ursula -user root -exec chown ursula:ursula {} \;
```

# PERM

```sh
sudo find / -user root -perm -4000 # show all files that hold the SUID-Bit and belong to root (most dangerous because those binaries will always be executed as root

find <path> -perm -<mode> # note the `-` preceding the permissions; files should have *at least* the given permission, not the exact permission (-mode)  
sudo find / -perm -4000 # show all files holding the SUID-Bit; the zeros tell the find command that any of the values are fine for the other permission bits  
sudo find / -perm -u+s # the same
```

# SIZE

```sh
find . -size +15M # files greater than 15MB
find . -size -15M # files smaller than 15MB
```

# TIME

```sh
# hours last status change
find . -ctime -1 # status was last changed less than 24 hours ago.
find . -ctime +1 # status was last changed more than 24 hours ago
find . -ctime +1 # status was last changed exactly 24 hours ago
# hours
find . -atime +1 #

# minutes last status change
find . -cmin -60 # File's status was last changed less than 60 minutes ago
find . -cmin +60 # File's status was last changed more than 60 minutes ago
# minutes last access
find . -amin -60 #
```

# PRUNE

```sh
find -prune <path> # if the file is a directory, do not descend into it. Cannot be used together with: -depth and -delete

find . -path ./src/emacs -prune -o -print # skip the directory *src/emacs* and all files and directories under it, and print the names of the other files found
find . -name temp -prune #  if there's a file with the name *temp* inside any *temp* directory, it would not have got printed.  
# -prune OR -print

find . -name <pattern> -prune -o -print # By default, the find command prints all the files matching the criteria. However, once the -print option is specified, it will print files only on explicit print instructions. In this find command, -print is associated in the other side of the OR condition, and hence nothing will get printed from the 1st part of the condition.
```

# EXEC

```sh
find <path> -test <pattern> -exec <cmd> \; 
# exec cmd
# all following arguments to find are taken to be arguments to command until `;` is encountered
# `;` has to be escaped by `\`
```

```sh
find <path> -test <pattern> -exec <cmd> {} \; 
# `{}` is replaced by the current file name being processed everywhere it occurs in the arguments to the command

# example:
find ./2024-01/maren_cam/ -name *.webp -exec echo "found {}" \;
```

```sh
find <path> -test <pattern> -exec <cmd> {} + # this variant runs the specified command on the selected files, but the command line is built by appending each selected file name at the end; the total number of invocations of the command will be much less than the number of matched files.  The command line is built in much the same way that xargs builds its command lines.  Only one instance of `{}' is allowed within the command, and (when find is being invoked from a shell) it should be quoted (for example, '{}') to protect it from interpretation by shells.  The command is executed in the starting directory.
```

```sh
find <path> <pattern> -execdir command {} # Like -exec, but the specified command is run from the subdirectory containing the matched file, which is not normally the directory in which you started find. As with -exec, the {} should be quoted if find is being invoked from a shell. This a much more secure method for invoking commands, as it avoids race conditions during resolution of the paths to the matched files.
```

## examples

```sh
# test:
sudo find /usr/share/icons -iname firefox.png -exec echo "Found: {}" \;

# rename files 
# where the found files are stored in {} 
# and then the same are renamed with _renamed extension
find / -type f -name 'file*' -exec mv {} {}_renamed \; 

# convert all .ttf files found starting from .
find . -name "*.ttf" -exec woff2_compress {} \; 
```

# FIND & XARGS

```sh
find -print0 # Print the full file name on the standard output, followed by a null character (instead of the newline character that -print uses).  This allows file names that contain newlines or other types of white space to be correctly interpreted by programs that process the find output. This option corresponds to the -0 option of xargs.
```

```sh
find 2024-01/maren_cam/ -name *.webp -print0 | xargs -0 identify -verbose | grep Filesize
```

# FIND & LOOPS

* Using `for` with `find` is the wrong approach here, see for example this [writeup](http://mywiki.wooledge.org/BashPitfalls#for_i_in_.24.28ls_.2A.mp3.29) about the can of worms you are opening.
* The recommended approach is to use `find`, `while` and `read` as described [here](http://mywiki.wooledge.org/BashFAQ/001).

```bash
# This way you delimit the filenames with null (`\0`) characters
# This means that variation in space and other special characters will not cause problems.

find . -type f -name '*.*' -print0 | 
while IFS= read -r -d '' file; do
    printf '%s\n' "$file"
done
```

```sh
# This works and is simpler:
find . -name '<pattern>' | while read LINE; do echo "$LINE" ; done
```

# OPERATORS

Mehrere Suchkriterien werden automatisch mit *UND* verknüpft. Eine *ODER*-Verknüpfung muss mit `-o` eingeleitet werden:  

```sh
find <path> <expr1> -o <expr2> # Or; expr2 is not evaluated if expr1 is true.

find <path> ! <expr> # True if expr is false. This character will also usually need protection from interpretation by the shell.  
find <path> -not <expr> # Same as ! expr (but not POSIX compliant)  

find . -type f -not -name "*.html" # find all files below the current subdirectory that are NOT named with the filename pattern *.html 
```
