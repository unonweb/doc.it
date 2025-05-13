# lastlog

**reports the most recent login of all users or of a given user**

formats and prints the contents of the last login log `/var/log/lastlog` file. The login-name, port, and last; login time will be printed. The default (no flags) causes lastlog entries to be printed, sorted by their order in `/etc/passwd`.

```sh
cat /var/log/lastlog
```

