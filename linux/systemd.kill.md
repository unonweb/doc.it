* sections: `[Service]`, `[Socket]`, `[Mount]`, `[Swap]`

```ini
[Service]
KillSignal=SIGTERM
KillSignal=SIGHUP
# Specifies which signal to use when stopping a service. This controls the signal that is sent as first step of shutting down a unit (see above), and is usually followed by SIGKILL (see above and below).
# Note that, right after sending the signal specified in this setting, systemd will always send SIGCONT, to ensure that even suspended tasks can be terminated cleanly.

RestartKillSignal=SIGTERM
RestartKillSignal=SIGHUP
# Specifies which signal to use when restarting a service. The same as KillSignal= described above, with the exception that this setting is used in a restart job. Not set by default, and the value of KillSignal= is used.
```
