
- https://www.freedesktop.org/software/systemd/man/latest/systemd-delta.html

```bash
systemd-delta # see all local configuration
systemd-delta /run # see all runtime configuration
systemd-delta systemd/system # see all system unit configuration changes
systemd-delta --type=extended /run/systemd/system # see all runtime "drop-in" changes for system units
```