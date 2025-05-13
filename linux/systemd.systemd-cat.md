**connect the stdin or stdout of a process to the journal**

If no parameter is passed, it writes everything it reads from stdin to the journal

```sh
systemd-cat -t <program>
systemd-cat --identifier <program>
systemd-cat -p <priority>
systemd-cat --priority <priority>
```

*emerg* (0); *alert* (1); *crit* (2); *err* (3); *warning* (4); *notice* (5); *info* (6); *debug* (7)

# example

```sh
echo '…bis hierhin lief es ja ganz gut' | systemd-cat -t *meinprogramm* -p info

if [ $_LOG_BASH_SOURCES == "true" ]; then
  systemd-cat -t bash echo "Parsed: $BASH_SOURCE"
fi
```
