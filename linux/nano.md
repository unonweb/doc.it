# Shotcuts

```sh
ctrl + g # help
```

# files

```sh
sudo nano /etc/nanorc
```

```sh
nano ~/.nanorc # or 
nano ~/nano/nanorc # or 
~/.config/nano/nanorc

/usr/share/nano/* # Syntax definitions for the syntax coloring of common file types
```

# config

```sh
man nanorc # help
```

```sh
mouse # When enabled, mouse clicks can be used to place the cursor, set the mark (with a double click), and execute shortcuts. The mouse will work in the X Window System, and on the console when gpm is running. Text can still be selected through dragging by holding down the Shift key. 
```

```sh
# Non-default settings
set tabsize 4       # Sets tab-to-spaces size to 4.
set tabstospaces    # Converts TAB key press to spaces.
set autoindent      # A new line will have the same number of leading spaces as the previous one.

set constantshow    # Displays useful information e.g. line number and position in the bottom bar.

set historylog      # Save the last 100 history searches for later use.
set positionlog     # Saves the cursor position between editing sessions.
set zap             # Allows you to highlight text (CTRL+SHIFT+ARROW) and delete it with backspace.

set indicator       # Displays a scroll bar on the right that shows the position and size of the current view port.
```

### maybe

```sh
set atblanks        # wrap line at blanks.
set speller "aspell -x -c"         # Sets what spelling utility to use.
set suspend         # Enables CTRL+Z to suspend nano.
set softwrap        # Enable softwrap of lines.

# Enable and set a working backup directory
set backup                              # When saving a file, create a backup file by adding a tilde (~) to the file's name
set backupdir "~/.cache/nano/backups/"  # The location of the backups.
```

### no

```sh
set minibar         # Displays file name and other information in the bottom bar. Removes top bar.
set cutfromcursor   # CTRL+K cuts from cursor position to end of line.
set linenumbers     # Lines are numbered.
set nohelp          # Disable the help information (CTRL+G to view the help screen).
```



# keybindings

```sh
bind key function menu # Rebinds the given key to the given function in the given menu (or in all menus where the function exists when all is used).
```

The format of key should be one of:

```sh
^X # where X is a Latin letter, or one of several ASCII characters (@, ], \, ^, _), or the word "Space".
M-X # where X is any ASCII character except [, or the word "Space". 
Sh-M-X # where X is a Latin letter. By default, each Meta+letter keystroke does the same as the corresponding Shift+Meta+letter.  But when any Shift+Meta bind is made, that will no longer be the case, for all letters.
```

```sh
chopwordleft 
# Deletes from the cursor position to the beginning of the preceding word.  (This function is bound by default to <Shift+Ctrl+Delete>.  If your terminal produces ^H  for  <Ctrl+Backspace>, you can make <Ctrl+Backspace> delete the word to the left of the cursor by rebinding ^H to this function.)

chopwordright 
# Deletes from the cursor position to the beginning of the next word.  (This function is bound by default to <Ctrl+Delete>.)

cutrestoffile 
# Cuts all text from the cursor position till the end of the buffer

cut
# Cuts and stores the current line (or the marked region).
            
zap 
# Throws away the current line (or the marked region).  (This function is bound by default to <Meta+Delete>.

execute 
# Prompts for a program to execute.  The program's output will be inserted into the current buffer (or into a new buffer when M-F is toggled)

indent
# Indents (shifts to the right) the current line or the marked lines.
unindent
# Unindents (shifts to the left) the current line or the marked lines.
            
whereis 
# forward search for text in the current buffer -- or for filenames matching a string in the current list in the file browser.
wherewas
# backward search for text in the current buffer -- or for filenames matching a string in the current list in the file browser.
```



```sh
# Shortcut key bindings
bind ^C copy main       # CTRC+C - Copy
bind ^V paste all       # CTRL+V - Past
bind ^X cut main
# bind ^X exit all

bind ^F whereis all     # CTRL+F - Find
bind ^G findnext all
bind ^D findprevious all
bind ^S savefile main   # CTRL+S - Save 


bind ^Z undo main
bind ^Y redo main
```

## troubles

```sh
# Preserve the XON and XOFF keys (^Q and ^S).
# set preserve
```

## syntax highlighting

* https://github.com/scopatz/nanorc

  Just copy the one you need and paste it into a new file under `/usr/share/nano/`.
  
  ```sh
  # .nanorc
  include "/usr/share/nano/*.nanorc" # Enables the syntax highlighting.
  ```

# example

```sh

```

