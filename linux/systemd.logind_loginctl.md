# SYSTEMD-LOGIND

- https://www.freedesktop.org/software/systemd/man/latest/logind.conf.html

```sh
systemctl status systemd-logind.service
```

# LOGINCTL

- https://www.freedesktop.org/software/systemd/man/latest/loginctl.html#

**manage sessions, view logged-in users, screen lock and unlock, kill sessions and processes of session**

* controls the system login manager - **systemd-logind**

The command operates these entities:

- **User**
  any user that is registered in Linux system
- **Session**
  user's authorization. Each login, except user switching using `su` or `sudo` commands, is a dedicated session. You  can log in using TTY, SSH or a graphical login manager and all those are new sessions;
- **Seat**
  this concept allows connecting multiple sets of hardware (mouse, keyboard, display) and using it by multiple users  at the same time. Usually, this feature is not used and the utility  shows only one seat.

```sh
loginctl list-users # shows user names who are currently logged in
loginctl user-status <id> # works as well as session-status, prints login date, state, and active services with their processes as a tree
loginctl show-user <id> # prints information about the user and his current session, without a list of services
loginctl terminate-user <id> # terminates all user sessions and their processes
loginctl kill-user <id> # sends a signal to all processes of a user. SIGTERM will be sent by default
```

```sh
loginctl list-sessions # shows a list of sessions
loginctl session-status <id> # shows the session state, login date and time, and a list of processes of the session
loginctl show-session <id> # shows properties of the session
loginctl activate <id> # switches to the specified session
lock-session <id> # locks screen for the specified session
unlock-session <id> # unlocks screen for the specified session
loginctl kill-session <id> # terminates the session or sends the specified signal to all session's processes
loginctl terminate-session <id> # terminates session
```

```sh
loginctl list-seats # shows a list of available seats
loginctl seat-status <id> # shows all processes that are related to the seat
loginctl show-seat <id> # prints settings for the seat
loginctl terminate-seat <id> # terminates all processes that are related to the seat.
```

## enable-linger

Systemd supports services that can be run from regular users, without superuser privileges. These services require to use the **--user** option with the **systemctl** command to interact with them. *Usually, user services start when the user logs in the first time and stop when he logs out*. But in some cases, you may want the user's services to be started at the system start and not to be killed at log out. You can configure it using the linger feature.

```sh
loginctl enable-linger frida
```
