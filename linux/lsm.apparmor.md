# LINKS

- https://www.youtube.com/watch?v=OzyalrOzxE8
- https://apparmor.pujol.io/
- https://github.com/roddhjav/apparmor.d
- https://gitlab.com/apparmor/apparmor/-/wikis/Documentation
# INFO

- 1998: Immunix
- 2005: Novell bis 2007
- 2009: Canonical (Wiederaufnahme)
- 2010: Integration im Linux-Kernel 2.6.

- **Distributionen**: openSuSE, Ubuntu
- Kernel: LSM enhancement
- bind access control attributes to programs rather than to users    
- path-based
- läuft als _apparmor.service_
- Es sollten alle Programme abgesichert werden die Netzwerkverkehr haben:
    - solche, die ständig Ports geöffnet haben
    - solche, die ad hoc Netzwerkverkehr verarbeiten (z.B. E-Mail-Anhänge öffnen)

# INSTALL

```sh
sudo apt install apparmor
sudo apt install apparmor-utils apparmor-notify 
sudo apt install apparmor-profiles apparmor-profiles-extra
```
# KERNEL

```sh
cat /sys/kernel/security/lsm 
# lockdown,capability,landlock,yama,apparmor,tomoyo,bpf%
sudo sysctl kernel/unprivileged_userns_apparmor_policy
# kernel.unprivileged_userns_apparmor_policy = 1
```

## boot options

enable apparmor as the default security module:

```sh
# /etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="apparmor=1 security=apparmor" # 
# /etc/default/grub.d/90-apparmor.cfg
GRUB_CMDLINE_LINUX_DEFAULT="$GRUB_CMDLINE_LINUX_DEFAULT apparmor=1 security=apparmor"
```

```sh
sudo update-grub
```

## PACKAGES

- **apparmor-profiles** 
  contains profiles managed by the upstream AppArmor community; experimental AppArmor profiles. Do not expect these profiles to work out-of-the-box. These profiles are not mature enough to be shipped in enforce mode by default on Debian. They are shipped in complain mode so that users can test them, choose which are desired, and help improve them upstream if needed.
  Some even more experimental profiles are included in 
  `/usr/share/doc/apparmor-profiles/extras/`
- **apparmor-profiles-extra**
  contains profiles developed by Ubuntu and Debian.
- **apparmor-utilities**
  provides utilities that operate on AppArmor profiles. Profiles can be created, updated, enforced, set to complain mode, and disabled with tools such as aa-audit, aa-autodep, aa-cleanprof, aa-complain, aa-decode, aa-disable, aa-enforce, aa-genprof, aa-logprof, aa-mergeprof, aa-unconfined, aa-update-browser
- **apparmor-notify**
  provides a utility to display AppArmor denial messages via desktop notifications. The utility can also be used to generate summary reports.
# PROFILE SYNTAX

## profile definition

name of the profile followed by an optional flags field:

```sh
/usr/bin/firefox [flags=(_f__lags_)] {
    # profile contents
}
```

If the profile name does not begin with a / then the keyword _profile_ should be prepended

```sh
profile user1 {
    ä profile contents
}
```

- Profile names can contain file rule globbing characters to allow them to apply to multiple executables

## include rules

Policy files can include other files to share text segments.

```
include
< > for text chucks relative to a set _include_ directory: #include <file>
" " for files relative to the current file: #include “a/relative/path/file”
```

### tunables

```sh
@string # variables defined by 
# abstractions: /etc/apparmor.d/abstractions/
#tunables: /etc/apparmor.d/tunables/
# or by the profile itself
```

### child profiles

Child profiles can be used to confine an application in a special way, or when you want the child to be unconfined on the system, but confined when called from the parent-

### hats

Hats are a special child profile that can be used with the change_hat API call. To denote a hat, prepend ^ before the hat name with no spaces. Eg:

```
/parent/profile {
    ^hat {
    }
}
```

## file globbing

```sh
/ # is never matched
* # match zero or more characters at the directory level (including hidden files)
** # match zero or more characters over multiple directory levels.
```

## rule modifiers

When there is no corresponding rule for a resource, AppArmor will block access to the resource and log it. When there is a rule in the policy, access is allowed to the resource without logging. The following modifiers can be prepended to a rule to change this behavior:

```sh
audit <path> # force logging
deny <path> # explicitly deny, without logging
audit deny <path> # combination to explicitly deny, but log
```

_**deny rules**_ are evaluated before allow rules and cannot be overridden by an allow rule. They are often used to override file globbing rules.

```sh
/var/www/* r, # allow read anything in _/var/www/_
deny /var/www/.htaccess r, # overrides the first rule, denying to read /var/www/.htaccess
```

## rule types

### capability rules

```
/profile {
    capability setuid,
    capability setgid,
}
```
### network rules

```sh
# a network daemon might need:
/profile {
    network inet dgram,
    network inet stream,
}
```

```sh
# a packet analyzer might need:
/profile {
    network raw,
    network packet,
}
```

# FILES

- convention of replacing `/` in pathnames with `.` (except for the root /)
- `/usr/sbin/nscd` profile would be named `usr.sbin.nscd`

```sh
ll /etc/apparmor.d # profiles; convention of replacing / in pathnames with .
sudo cat /sys/kernel/security/apparmor/profiles # the profiles loaded into the kernel
```

# CLI

```sh
aa-enabled # tests whether AppArmor is enabled
aa-teardown # unloads all AppArmor profiles

aa-exec [options] <executable> # execute a binary confined by the specified profile and/or namespace.
# If both a profile and namespace are specified, command will be confined by profile in the new policy namespace. If only a namespace is specified, the profile name of the current confinement will be used. If neither a profile or namespace is specified command will be run using standard profile attachment (ie. as if run without the aa-exec command)

-d /path/to/profiles # specifies where to look for the AppArmor security profile set; defaults to /etc/apparmor.d

-p or --profile <profile> # confine command with profile; if not specified use the current profile name (likely unconfined).
-n or --namespace <namespace> # use profiles in namespace; will result in confinement transitioning to using the new profile namespace.
```

## monitoring

```sh
sudo aa-status # displays various information about the current AppArmor policy; uses the /proc filesystem to determine which processes are confined.

sudo aa-unconfined # list the programs which have no associated profile and which expose an open network socket.

sudo aa-unconfined --paranoid # get all unconfined processes that have at least one active network connection.
```

### aa-notify

- needs to be able to read the logfiles containing the AppArmor DENIED messages.

```sh
sudo aa-notify # displays a summary or provide desktop notifications for (logged) AppArmor _denied_ messages.

sudo aa-notify -p
sudo aa-notify --poll # poll AppArmor logs and display desktop notifications. Can be used with -s option to display a summary on startup.

sudo aa-notify -l
sudo aa-notify --since-last # show summary since last login

sudo aa-notify -s
sudo aa-notify --since-days <number> # show summary for last _number_ of days

sudo aa-notify -v
sudo aa-notify --verbose # show messages with summaries

sudo aa-notify --display $DISPLAY # set the DISPLAY environment variable to $DISPLAY (might be needed if sudo resets $DISPLAY)
```

#### config

```sh
sudo nano /etc/apparmor/notify.conf # system-wide
nano ~/.apparmor/notify.conf # user
```

```sh
show_notifications="yes" # set to 'yes' to enable AppArmor DENIED notifications
use_group="admin" # only people in _use_group_ can use aa-notify
```
## profiles

- **enforcement** # Profiles loaded in enforcement mode will result in enforcement of the policy defined in the profile as well as reporting policy violation attempts to syslogd.
- **complain** # Profiles loaded in _complain_ mode will not enforce policy. Instead, it will report policy violation attempts. This mode is convenient for developing profiles. Complain mode status will still enforce any explicit deny rules in a profile

```sh
aa-audit <executable> # set an AppArmor security profile to audit mode; in this mode security policy is enforced and all access (successes and failures) are logged to the system log.

aa-enforce <executable> [-d /path/to/profiles] # set one or more profiles to enforce mode; only relevant in conjunction with the aa-complain utility and the aa-disable utility. The default mode for a security policy is enforce and the aa-complain utility must be run to change this behavior.

aa-disable <executable> # disable one or more profiles; will unload the profile from the kernel and prevent the profile from being loaded on AppArmor startup. The aa-enforce and aa-complain utilities may be used to to change this behavior.

aa-complain <executable> # set the enforcement mode for one or more profiles to complain mode. In this mode security policy is not enforced but rather access violations are logged to the system log. Note that 'deny' rules will be enforced even in complain mode.
```

```sh
# list available profiles
ll /usr/share/apparmor/extra-profiles/ | less
# install the profile
sudo cp /usr/share/apparmor/extra-profiles/usr.bin.example /etc/apparmor.d/
# set the profile to complain mode
sudo aa-complain /etc/apparmor.d/usr.bin.example
```
### create mechanism

1. first console: `aa-genprof <application>`
2. second console: use _application_ # if it's a daemon _systemctl start/stop application_
3. when finished, run the profile in complain mode for some time; then use `aa-logprof` to update the profile
4. when ready: `aa-enforce <application>`

### create: aa-genprof

- apparmor-utilities
- interactive profile generation utility for AppArmor

Helps when creating a profile for an app the first time so that AppArmor can learn what the applications tendencies are and prompt you for what behavior needs to be taken in certain circumstances.

If a profile does not exist for the program, `aa-genprof` will create one using _aa-autodep_.

Genprof will then:
- set the profile to complain mode
- write a mark to the system log
- instruct the user to start the application to be profiled in another window and exercise its functionality

If the user selects (S)can, aa-genprof will parse the complain mode logs and iterate through generated violations using _aa-logprof_.

```sh
sudo aa-genprof <executable> #
```

### update: aa-logprof

- apparmor-utilities
- **utility for updating AppArmor security profiles**

useful once you have an existing profile and need to allow/deny access to certain tasks which have already been logged during enforce or complain modes.

an interactive tool used to review AppArmor generated messages and update AppArmor security profiles; running `aa-logprof` will scan the log file and if there are new AppArmor events that are not covered by the existing profile set, the user will be prompted with suggested modifications to augment the profile.

When `aa-logprof` exits profile changes are saved to disk. If AppArmor is running, the updated profiles are reloaded and if any processes that generated AppArmor events are still running in the null-complain-profile, those processes are set to run under their proper profiles.

prompts if the app is attempting to _**access or execute a certain file**_ OR requesting _**access to a certain capability from the kernel**_.

```sh
sudo aa-logprof
sudo aa-logprof -d
sudo aa-logprof --dir /path/to/profiles # Specifies where to look for the AppArmor security profile set. Defaults to /etc/apparmor.d
sudo aa-logprof -f
sudo aa-logprof--file /path/to/logfile # Specifies the location of logfile that contains AppArmor security events. Default locations are read from /etc/apparmor/logprof.conf
# Typical defaults are:
# /var/log/audit/audit.log
# /var/log/syslog
# /var/log/messages
```

### cleanup: aa-cleanprof

```sh
sudo aa-cleanprof <executable> # removes any existing superfluous rules (rules that are covered under an include or another rule), reorders the rules to group similar rules together and removes all comments from the file.
```

## apparmor-easyprof

provides the aa-easyprof utility which is an easy to use interface for AppArmor policy generation. aa-easyprof supports the use of templates and policy groups to quickly profile an application.