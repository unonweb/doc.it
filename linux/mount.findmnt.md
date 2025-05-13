
```sh
findmnt -N
findmnt --task <pid> # Use alternative namespace /proc/<tid>/mountinfo rather than the default /proc/self/mountinfo
findmnt --source <spec> # Explicitly define the mount source. Supported specifications are device, maj:min, LABEL=label, UUID=uuid, PARTLABEL=label and PARTUUID=uuid.
findmnt --mountpoint <path> # Explicitly define the mountpoint file or directory.
```

# OUTPUT

```bash
findmnt --output <list> # Define output columns. See the --help output to get a list of the currently supported columns
findmnt --help

findmnt --list --output SOURCE,TARGET | grep /mnt/debian
```