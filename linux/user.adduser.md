# adduser / addgroup

high level utility; not standard; Debian

* `adduser` will copy files from `/etc/skel/` into the home directory

# CLI
## normal user

```sh
sudo adduser <user> # interaktiv; legt ein Passwort an; shell = bash; 
# erstellt user; will choose the first available UID from the range specified for normal users (default: 1000-59999)

sudo adduser <user> --add_extra_groups # adds the new user to the extra groups defined in the configuration file
sudo adduser <user> --ingroup <group> # adds the new user to group instead of a usergroup or the default group defined by USERS_GID in the configuration file; affects the users primary group
sudo adduser <user> --disabled-login # do not run passwd to set the password; the user won't be able to use her account until the password is set
sudo adduser <user> --disabled-password # like --disabled-login, but logins are still possible (for example using SSH RSA keys) but not using password authentication
sudo adduser <user> --shell <path> # use the shell with path as the user's login shell, rather than the default specified by the configuration file; z.B. /usr/sbin/nologin
sudo adduser <user> --uid <ID> # Force the new userid to be the given number. adduser will fail if the userid is already taken.
```

## system user

```sh
sudo adduser --system user
# default: group = nogroup; shell = /usr/sbin/nologin
# will choose the first available UID from the range specified for system users (default: 100-999)
sudo adduser <user> --no-create-home # do not create the home directory
```

## nobody-like user

```sh
sudo adduser --system --no-create-home <user> # group = nogroup
sudo addgroup <group>; adduser <user> <group> # creates group and adds the new user 
sudo adduser --no-create-home --shell /usr/sbin/nologin --disabled-login <user> # 
```

## add user to group

```sh
sudo adduser <user> <group> # fügt user group hinzu
sudo adduser frida sudo #
su --login $USER # necessary to reload a user's group assignments
# login-specific resource files such as .profile or .login will be read by the shell
# the /home directory will be changed
```

## add a group

```sh
sudo adduser --group # a group with the given name is created. This is the default action
sudo adduser --group --system # a group with the same name and ID as the system user is created
```

# CONFIG

```sh
# /etc/adduser.conf

USERGROUPS=yes # if yes each created user will be given their own group to use as a default. If no, each created user will be placed in the group with the id defined with USERS_GID=

EXTRA_GROUPS="dialout cdrom floppy audio video plugdev users" # list of groups that new non-system users will be added to if the *--add_extra_groups* is used
```
