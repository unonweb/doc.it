# XDG Base Directory Specification

- https://specifications.freedesktop.org/basedir-spec/latest/
- https://wiki.gnome.org/Initiatives(2f)GnomeGoals(2f)XDGConfigFolders.html

## environment variables

- `$XDG_DATA_HOME`:
  The base directory relative to which **user-specific data** files should be stored.
  Default: `$HOME/.local/share`
- `$XDG_CONFIG_HOME`:
  The base directory relative to which user-specific **configuration files** should be stored.
  Default: `$HOME/.config`
- `$XDG_STATE_HOME`:
  Defines the base directory relative to which user-specific **state files** should be stored.
  Default: `$HOME/.local/state`
- `$XDG_CACHE_HOME`:
  Defines the base directory relative to which user-specific non-essential data files should be stored.
  Default: `$HOME/.cache`
- `$HOME/.local/bin`:
  User-specific executable files.
### defaults

```sh
XDG_DATA_HOME=$HOME/.local/share
XDG_CONFIG_HOME=$HOME/.config
XDG_STATE_HOME=$HOME/.local/state
XDG_CACHE_HOME=$HOME/.cache
```

## search paths

separator = `:`

- `$XDG_DATA_DIRS`:
  Defines the preference-ordered set of base directories to search for **data files** in addition to the `$XDG_DATA_HOME` base directory.
  Default: `/usr/local/share/:/usr/share/`
- `$XDG_CONFIG_DIRS`:
  Defines the preference-ordered set of base directories to search for **configuration files** in addition to the `$XDG_CONFIG_HOME` base directory. 
  Default: `/etc/xdg`

### defaults

```sh
XDG_DATA_DIRS=/usr/local/share/:/usr/share/
XDG_CONFIG_DIRS=/etc/xdg
```

# xdg-user-dirs

- https://wiki.archlinux.org/title/XDG_user_directories

is a tool to help manage "well known" user directories like the desktop folder and the music folder. It also handles localization (i.e. translation) of the filenames.

The way it works is that `xdg-user-dirs-update` is run very early in the login phase. This program reads a configuration file, and a set of default directories. It then creates localized versions of these directories in the user home directory and sets up a configuration file in `$XDG_CONFIG_HOME/user-dirs.dirs` that applications can read to find these directories.

Most file managers indicate XDG user directories with special icons. 

```sh
nano ~/.config/user-dirs.dirs # local
sudo nano /etc/xdg/user-dirs.defaults # global

xdg-user-dirs-update # update
xdg-user-dir MUSIC # show
```