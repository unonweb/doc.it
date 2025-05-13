# NOTES

```sh
cp /home/frida/test /home/payload/test # copy across different home directories only works if the current user has access to the entire destination path
```

## example: publishEvent.mjs

requirements:

- Shall be **executable but not readable** to normal users because it holds secrets (api-key). That means it cannot be owned by the normal user who executes it.
- Shall **not run as root** because it calls nodejs and it accepts user input.

solution:

- /usr/share/applications/publishEvent.desktop 
  calls as root: /opt/publishEvent/publishEvent.mjs
  --> change to /etc/systemd/system/fk.publish-event.service

* fk.publish-event.service:
  DynamicUsers=true

- script in /opt/publishEvent/publishEvent.mjs
- it is owned by root but not run by root!


## security

```sh
sudo find / -user root -perm -4000 # show all files that hold the SUID-Bit and belong to root (most dangerous because those binaries will always be executed as root
```

# GROUPS

- **Group**: A group is a collection of users who can share files and other system resources
- **Primary Group**: Specifies a group that the operating system assigns to files that the user creates. Naturally, each user must belong to a primary group
- **Secondary Group**: Specifies one or more groups to which a user also  belongs. In addition, users can belong to up to 15 secondary groups

When we create a new user in Linux, a new group with the same name as the user will also be created and associated with the user. 
This new group is the user’s primary group.

## system groups

The following groups grant privileges to their members. 

### debian

- **lp** (LP): Members of this group can enable and use printers. (The user **lp** is not used anymore.) 
- **lpadmin** (LPADMIN): Allows members to manage printers and pending jobs sent by other users. 
- **scanner** : Members of this group can enable and use scanners. 
- **adm**: Group adm is used for system monitoring tasks. Members of this group  can read many log files in /var/log, and can use xconsole. Historically, /var/log was /usr/adm (and later /var/adm), thus the name of the group. 
- **systemd-journal**: Since Debian 8 (Jessie), members of this group can use the command *journalctl* and read log files of systemd (in /var/log/journal). 
- **plugdev**: Allows members to mount (only with the options nodev and nosuid, for  security reasons) and umount removable devices through pmount. 
- **netdev**: Members of this group can manage network interfaces through the network manager and wicd. 
- **cdrom**: This group can be used locally to give a set of users access to a CDROM drive and other optical drives. 
- **floppy**: This group can be used locally to give a set of users access to a  floppy drive and other removable (non-optical) drives (like USB flash  drives). Starting with Debian 8 (Jessie) it is not used anymore for USB  and flash memories. 
- **tape**: This group can be used locally to give a set of users access to a tape drive. 
- **audio**: This group can be used locally to give a set of users access to an audio device (the soundcard or a microphone). 
- **video**: This group can be used locally to give a set of users access to a video device (like a webcam). 
- **render**: This group can be used locally to give a set of users access to a rendering device (like the framebuffer, or videocard). 
- **sudo**: Members of this group can execute any command with sudo or pkexec. (See the default configuration in /etc/sudoers) 
- **tty**: TTY devices are owned by this group. This is used by write and wall to  enable them to write to other people's TTYs, but it is not intended to  be used directly. 
- **staff**: Allows users to add local modifications to the system (/usr/local)  without needing root privileges (note that executables in /usr/local/bin are in the PATH variable of any user, and they may "override" the  executables in /bin and /usr/bin with the same name). Compare with group "adm", which is more related to monitoring/security. 
- **shadow**: /etc/shadow is readable by this group. Some programs that need to be able to access the file are SETGID shadow. 
- **utmp**: This group can write to /var/run/utmp and similar files. Programs that need to be able to write to it are SETGID utmp. 
- **disk**: Raw access to disks. **Mostly equivalent to root access.** 
- **kmem**: /dev/mem and /dev/port are readable by this group. This is mostly a BSD relic, but any programs that need direct read access to the system's  memory can thus be made SETGID kmem. /dev/kmem, that has given the name  to this group, was also readable by this group, but is currently  disabled by default for security reasons. 
- **dialout**: Full and direct access to serial ports. Members of this group can reconfigure the modem, dial anywhere, etc. 
- **dip**: The group's name stands for "Dial-up IP", and membership in dip allows  you to use tools like ppp, dip, wvdial, etc. to dial up a connection.  The users in this group cannot configure the modem, but may run the  programs that make use of it. 
- **fax**: Allows members to use fax software to send / receive faxes. 
- **voice**: Voicemail, useful for systems that use modems as answering machines. 
- **users**: While Debian systems use the private user group system by default (each user has their own group), some prefer to use a more traditional group  system, in which each user is a member of this group. 
- **wheel**: This group can be created to alter the functionality of the module  pam_wheel in /etc/pam.d/su to restrict the execution of su. If this  group does not exists only members of **root** (usually  root itself) are affected. By default this group does not exist and all  the configurations with pam_wheel are commented in /etc/pam.d/su. See  the page about the wheel_pam module for more information. 
- **src**: This group was used to manage the source code of the system, including files in /usr/src. It is not used anymore. 
- **bluetooth**: Users in this group can talk with bluetooth service via dbus, as defined in `/etc/dbus-1/system.d/bluetooth.conf`. 
- **libvirt**: Users in this group can talk with libvirt service via dbus, as defined in `/etc/libvirt/libvirtd.conf`. 
- **kvm**: Users in this group can use the KVM acceleration of virtual machines. 

## files

```sh
/etc/group # enthält Benutzer Gruppenzugehörigkeiten
# Gruppenname : [Passwort] : GID : Gruppenmitglieder

/etc/passwd # zentrale Benutzerdatenbank
# Benutzername : [Passwort] : UID : GID : Info : Heimatverzeichnis : Shell
```

## groups

```sh
groups # shows groups of current user/process
groups <user> # shows <user>'s groups
cat /etc/group # show all groups
```

# OWNER

## UID / GID

* Die Zugriffsberechtigungen sind an die UID geknüpft; wenn ein System neu installiert wird, die alten Daten aber behalten werden, muss jedem Benutzer dieselbe UID gegeben werden, wie zuvor.
* system user: UID < 1000
* user IDs:
    * RUID: real 
    * EUID: effective
    * SUID: saved

```sh
# FILE: show_user_ids.sh

#!/bin/bash
echo -e "UID: $UID\nEUID: $EUID\nRUID: $RUID\nFSUID: $FSUID"

# USE:
show_user_ids.sh
sudo show_user_ids.sh
```
### real vs effective

The distinction between a real and an effective user id is made because you may have the need to temporarily take another user's identity (most of the time, that would be root, but it could be any user). If you only had one user id, then there would be no way of changing back to your original user id afterwards

* The ***real user id is who you really are*** (the one who owns the process)
* The ***effective user id is used for access checks*** (most of the time, there are some exceptions).

When you launch a process, it runs with the same permissions as the user or group that ran it, this is known as an **effective user ID**. This UID is used to grant access rights to a process. So naturally if Bob ran the touch command, the process would run as him and any files he created would be under his ownership.

There is another UID, called the **real user ID** this is the ID of the user that launched the process. These are used to track down who the user who launched the process is.

## SUID

One last UID is the **saved user ID**, this allows a process to switch between the effective UID and real UID, vice versa. This is useful because we don't want our process to run with elevated privileges all the time, it's just good practice to use special privileges at specific times.

When running `passwd`, your effective UID is your user ID, let's say its 500 for now. Oh but wait, remember the passwd command has the SUID permission enabled. So when you run it, your effective UID is now 0 (0 is the UID of root). Now this program can access files as root.

**SUID** (saved user ID, suid): … is used when a program running with elevated privileges needs to do some unprivileged work temporarily; changing euid from a privileged value (typically 0) to some unprivileged value causes the privileged value to be stored in suid. Later, a program's euid can be set back to the value stored in suid, so that elevated privileges can be restored; an unprivileged process may set its euid to one of only three values: the value of ruid, the value of suid, or the value of euid

# ROOT

## root vs sudo

**Debian**: root hat kein pw und kann nicht konfiguriert werden // brute force nicht möglich // primärer User darf sudo bedingungslos nutzen

* **Möglichkeit 1**: Root-Account deaktiviert lassen und das System ausschließlich über `sudo` administrieren

  ```sh
  vim /etc/passwd
  # root:x:0:0:root:/root:/sbin/nologin
  # From now on, when root user logs in, he/she will get the message “This account is currently not available.”
  
  sudo vim /etc/ssh/sshd_config
  # PermitRootLogin no
  ```

  <u>Vorteil</u>: without root, attackers must first guess a user name configured as a sudoer as well as the user password.

  <u>Nachteil</u>: Wenn nicht-vertrauenswürdige Benutzer direkten Zugang zum Rechner haben, können sie ihn über den Bootmanager Grub im Recovery Modus starten. Ist der Root-Account deaktiviert, erhält ein böswilliger Benutzer so ohne Passwortabfrage eine Root-Shell. Wird der Rechner in einer Multiuser-Umgebung eingesetzt, z.B. im Rechnerraum einer Schule oder Universität, sollte der Root-Account daher aktiviert werden.

- **Möglichkeit 2**: Root-Account aktivieren und mit eigenem Passwort sichern

  ```sh
  # /etc/sudoers
  Defaults targetpw # configures sudo to ask for the root password instead of the user password 
  # by adding 'targetpw' or 'rootpw' to the 'Defaults' line
  ```

  ```sh
  sudo passwd root # gibt root ein Passwort
  passwd -l root # sperrt den root-Acount (ensure a user is properly configured as a sudoer before disabling the root account!)
  ```
## troubles

**This account is currently not available.**

```sh
grep root /etc/passwd
# root:x:0:0:root:/root:/sbin/nologin

sudo nano /etc/passwd # --> /bin/bash
```

# USERS

* One of the purposes for having the concept of users in the system is to **manage isolation for processes and resources**

## user ID

* by convention, **user IDs smaller than 1000 are reserved for system users**

* `0`: The **root** user is a special case in that it has a login shell, despite its user ID belonging to the range of system user IDs

* `1-99`: **system users**

* `100-999`: **application users** are system users that application programs create

* `1000-60000`: **regular users**

  ```sh
  sudo nano /etc/login.defs # UID_MIN and UID_MAX define the range
  ```

## hide user from display manager

```sh
sudo nano /var/lib/AccountsService/users/<user> # replace <user> with the username you want to hide from the login screen
```

Add the following to the file:

```sh
[User]  
Language=   
XSession=gnome  
SystemAccount=true  
```

# show

## users

```sh
cat /etc/passwd # list all users
cat /var/log/auth.log # alle Versuche, sich am System anzumelden, werden hier protokolliert. Dabei ist egal, ob sich jemand an einer Konsole oder über ssh anmeldet oder versucht wird, per sudo Root-Rechte zu erlangen.
```

# logout

```sh
gnome-session-quit
```

## lslogins 

display information about known users in the system

