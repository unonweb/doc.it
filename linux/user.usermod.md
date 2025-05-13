usermod / groupmod
===================

## change login name

```sh
usermod -l <new-name> <user>
usermod --login <new-name> <user> 
# Nothing else is changed. In particular, the user's home directory or mail spool should probably be renamed manually to reflect the new login name.
```

## change uid

```sh
usermod -u <uid> <user>
usermod --uid <uid> <user> 
# The new numerical value of the user's ID. This value must be unique, unless the -o option is used. The value must be non-negative. The user's mailbox, and any files which the user owns and which are located in the user's home directory will have the file user ID changed automatically. The ownership of files outside of the user's home directory must be fixed manually. No checks will be performed with regard to the UID_MIN, UID_MAX, SYS_UID_MIN, or SYS_UID_MAX from `/etc/login.defs`.
```

## change gid

```sh
groupmod -g <gid>
groupmod --gid <gid> 
# The group ID of the given GROUP will be changed to GID. The value of GID must be a non-negative decimal integer. This value must be unique, unless the -o option is used. Users who use the group as primary group will be updated to keep the group as their primary group. Any files that have the old group ID and must continue to belong to GROUP, must have their group ID changed manually. No checks will be performed with regard to the GID_MIN, GID_MAX, SYS_GID_MIN, or SYS_GID_MAX from /etc/login.defs.
su --login $USER # necessary to reload a user's group assignments
```

```sh
sudo groupmod -g 1000 frida && sudo usermod -u 1000 frida
```

## lock account

```sh
usermod -L
usermod --lock # soll das Konto gesperren werden (und nicht nur der PW-Zugang), muss auch das Verfallsdatum auf `1` gesetzt werden.  
usermod -U
usermod --unlock
```

## move home

```sh
usermod -m -d <path> <user> 
# verschiebt den Inhalt des Home-Verzeichnisses eines Benutzers zu dem neuen Ziel das mit -d angegeben wird:

usermod -m -d /home/newhome testuser`
usermod --move-home --home /home/newhome testuser
```

## change primary group

```sh
usermod -g <group> <user>
usermod --gid <group> <user> 
# ändert die primäre Gruppe von user in group. Der Gruppenname muss existieren. Jede Datei im Home-Verzeichnis des Benutzers, die der alten Hauptgruppe des Benutzers gehörte, wird dieser neuen Gruppe gehören. Die Gruppenzugehörigkeit von Dateien außerhalb des Home-Verzeichnisses des Benutzers muss per Hand angepasst werden.
```

## change additional groups

```sh
usermod -G <group1,group2> <user> # separator: comma (no whitespace)
usermod --groups <group1,group2> <user> # new list of supplementary GROUPS

usermod -aG <group1,group2> <user> # append to groups without removing from others
usermod --append --groups <group1> <group2> <user> 
```

## change group name

```sh
groupmod -n <group>
groupmod --new-name <group>
# ändert den Gruppenname unter Beibehaltung der GID und der Mitglieder
```
