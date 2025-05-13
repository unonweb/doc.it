While `su` switches you to the root user account and requires the root account’s password, 
`sudo` runs a single command with root privileges – it doesn’t switch to the root user or require a separate root user password.

The default security policy is `etc/sudoers`

# CLI
## options

```sh
sudo --login # öffnet eine Rootshell; sudo -i
# login-specific files such as .profile or .login will be read by the shell

sudo --preserve-env # Benutzerumgebung (Variablen) beibehalten; sudo -E
sudo --role <role> # Run the command with an SELinux security context that includes the specified role; sudo -r
```

```sh
sudo --set-home # Request that the security policy set the HOME environment variable to the home directory specified by the target user's password database entry. Depending on the policy, this may be the default behavior.
```



```sh
sudo --user <user> # führt ein Kommando als user aus; sudo -u
```

```sh
sudo --list # lists the allowed/forbidden commands for the invoking user; a longer list format is used if this option is specified multiple times; sudo -l
sudo -lU <user> #  lists the allowed/forbidden commands for user
```

## troubles

* **sudo & shell builtins**: only applies to programs; `cd` for example is not a program, but a shell builtin.

## edit files with sudo

```sh
sudo -e
sudo --edit # Edit one or more files instead of running a command. In lieu of a path name, the string "sudoedit" is used when consulting the security policy.  
```

If the user is authorized by the policy, the following steps are taken:

1. Temporary copies are made of the files to be edited with the owner set to the invoking user.
2. The editor specified by the policy is run to edit the temporary files.  The sudoers policy uses the **SUDO_EDITOR**, **VISUAL** and **EDITOR** environment variables (in that order).  If none of them are set, the first program listed in the editor sudoers option is used.
3. If they have been modified, the temporary files are copied back to their original location and the temporary versions are removed.

To help prevent the editing of unauthorized files, the following restrictions are enforced unless explicitly allowed by the security policy:

- Symbolic links may not be edited
- Symbolic links along the path to be edited are not followed when the parent directory is writable by the invoking user unless that user is root (version 1.8.16 and higher).
- Files located in a directory that is writable by the invoking user may not be edited unless that user is root (version 1.8.16 and higher).

Users are never allowed to edit device special files.

If the specified file does not exist, it will be created. Note that unlike most commands run by *sudo*, the editor is run with the invoking user's environment unmodified. If, for some reason, *sudo* is unable to update a file with its edited version, the user will receive a warning and the edited copy will remain in a temporary file.

# CONFIG
## sudoers

- https://toroid.org/sudoers-syntax
- [img](obsidian://open?vault=doc&file=_img%2F1_HZD98tKbfoMAeQdI2hTYiQ.webp)
- `/etc/sudoers`

```sh
sudo visudo # edit safely the sudoers file
# visudo locks the sudoers file, saves edits to a temporary file, and checks that file's grammar before copying it to /etc/sudoers
ll /etc/sudoers.d/
```

```sh
user host=(runas) command
# user = usernames command can be run as
# user may be specified as (user_ownership : group_ownership)
# user may be omitted
# host may be a hostgroup as well

%group host = ( user_ownership : group_ownership) commands
%webteam cms051=/usr/bin/systemctl restart httpd.service
```

### runas

controls the target user (and group) sudo will run the command as, or in other words, which combinations of the -u and -g arguments it will accept.

**If omitted, the user will be permitted to run commands only as root**. If you specify a username, e.g., (postgres), sudo will accept “-u postgres” and run commands as that user. In both cases, sudo will not accept -g.
### options

```sh
env_keep
# Environment variables to be preserved in the user's environment when the env_reset option is in effect.
# This allows fine-grained control over the environment sudo-spawned processes will receive.
# The argument may be a double-quoted, space-separated list or a single value without double-quotes.
# The list can be replaced, added to, deleted from, or disabled by using the =, +=, -=, and ! operators respectively.
# The /global list of variables to keep is displayed when sudo is run by root with the -V option
```
### example

```sh
# operator may run maintenance commands and anything in /usr/oper/bin/
operator    ALL = DUMPS, KILL, SHUTDOWN, HALT, REBOOT, PRINTING, sudoedit /etc/printcap, /usr/oper/bin/
```

```sh
# any user in the adm group on any host may run any command as any user without a password
%adm ALL=(ALL) NOPASSWD: ALL
```

```sh
# User alias specification
User_Alias FULLTIMERS = albert, ronald, ann# Runas alias specification
Runas_Alias	OP = root, operator
Runas_Alias	DB = oracle, mysql# Host alias specification
Host_Alias	PRODSERVERS = master, mail, www, ns
Host_Alias	DEVSERVERS = testdb, devapp1, preprod# Cmnd alias specification 
Cmnd_Alias	REBOOT = /usr/sbin/reboot
Cmnd_Alias	VIEWSHADOW = /usr/bin/cat /etc/shadow

# User specification
# root and users in group wheel can run anything on any machine as any user
root ALL=(ALL) ALL
%wheel ALL=(ALL) ALL

# full time users can run anything on any machine without a password
FULLTIMERS ALL=NOPASSWD:ALL
peter		DEVSERVERS = ALL # peter may run anything on machines in DEVSERVERS
jane		PRODSERVERS = /usr/bin/passwd [A-z]*, !/usr/bin/passwd root # jane may change passwords for anyone (except root) on PRODSERVERS
sam		DEVSERVERS = (DB) ALL # sam may run anything on the DEVSERVERS as DB owns(oracle, mysql).
fred		ALL = (DB) NOPASSWD: ALL
jen		ALL, !PRODSERVERS = VIEWSHADOW # jen can run VIEWSHADOW commands on all machines except the ones PRODSERVERS
```

* **User_Alias**: We can specifies a group of users by username; simply which users can use “sudo” as a prefix for commands.
* **Runas Alias**: This specifies a group of users by UID. We can define which accounts are authorized to “run commands as”. This allows sam user to run any command on DBSERVERS hosts as either oracle user or mysql user.

## visudo

**“What now?”** prompt. At this point the user may enter `e` to re-edit the  sudoers file, `x` to exit without saving the changes, or `Q` to quit and save changes. The ‘Q’ option should be used with extreme care because  if visudo believes there to be a parse error, so will sudo and no one  will be able to sudo again until the error is fixed.

# NOTES

## security

### write & execute

Users should never be granted sudo privileges to execute files that are writable by the user or that reside in a directory that is writable by the user. If the user can modify or replace the command there is no way to limit what additional commands they can run.

**Situation**: Der Gruppe *veracrypt* wird in *sudoers* das Recht eingeräumt `/usr/bin/veracrypt` auszuführen. 

**Problem**: ein Angreifer, der einen User-Account kapert, welcher in dieser Gruppe ist, könnte die ausführbare Datei ersetzen und jeden Code mit privilegierten Rechten ausführen. 

**Lösung**: Die Gruppe veracrypt darf in `/usr/bin/` auf keinen Fall schreibrechte bekommen!

## aliases with sudo

* The aliases are user specific - you need to define them in `/root/.bashrc`
* Problem (1): Bash does not load root profile when `sudo command` is invoked
* Problem (2): Bash only checks the first word of a command for an alias, any words after that are not checked. 
  That means in a command like `sudo ll`, only the first word (`sudo`) is checked by bash for an alias, `ll` is ignored. We can tell bash to check the next word after the alias (i.e `sudo`) by adding a space to the end of the alias value.

To ensure that our aliases are recognized and utilized when using sudo, we can add the following line to our `~/.bashrc` or `~/.bash_aliases`:

```bash
alias sudo='sudo ' # 
```

Note the trailing space in the alias. This adjustment allows our aliases to be expanded when executing commands with the *sudo* alias.

We may think instead of putting regular aliases into the root user’s */root/.bashrc* file. But this is ineffective unless we’re logged in directly as the root user, and so, it doesn’t solve our problem.

## pros / cons

### advantages

- Assign certain users full administrative privileges, while assigning other users only the privileges they need to perform tasks they need to do
- Allow users to perform administrative tasks by entering their own normal userpasswords so that you don't have to distribute the root password to everybody
- Make it harder for intruders to break into your systems. If you implement sudo and disable the root user account, would-be intruders won't know which account to attack because they won't know which one has admin privileges.
- Improve your auditing capabilities because you'll be able to see what users are doing with their admin privileges

### problems

* lacks efficient, centralized administration, causing system administrators to spend lots of time building and distributing sudoers files across the server.
* introduces a security risk because it is controlled by local files. The security admins need to appropriately distribute these files. Also, the sudo configuration file is stored in a way that local administrators could easily make modifications… a big security risk.
* may create a compliance issue. Auditors don’t like the distributed sudo configuration files because they utilize “static trust.” Because of this, your use of sudo may cause you problems passing audits.
* organizations using sudo must be able to distribute the file, so a good distribution method must be maintained, resulting in more costs to your organization.
* does not inherently provide the ability to link multifactor authentication (MFA) as part of the privileged user authorization process. 

## distro differences

- **Debian**: if you specify a root password during installation *sudo* is not installed by default; if you do not specify a root password, *sudo* is installed.

- **CentOS**: users in the group *wheel* are granted with *sudo* access; if you want to configure *sudo* for an existing user, just add your user to the wheel group: 

  ```sh
  usermod -aG wheel <username>
  ```
