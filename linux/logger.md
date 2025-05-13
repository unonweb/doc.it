# logger

**makes entries in the system log**;

```sh
logger --tag <tag> # marks every line to be logged with the specified *tag;* default tag = username; logger -t
logger --tag Backup "Datensicherung ausgeführt" # schreibt am Ende eines eigenen Backupt-Skripts diese Meldung in den Log.
logger --journald [<file>] # writes a systemd journal entry; the entry is read from *file*, if specified, otherwise from sdtin; each line must begin with a field that is accepted by journald;  see man systemd.journal-fields; Alternative: systemd-cat
```

