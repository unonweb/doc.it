# du

estimate file space usage

```sh
du -sch # 

du --summarize # display only a total for each argument; du -s
du --total # produce a grand total; du -c
du --human-readable # print sizes in human readable format (e.g., 1K 234M 2G); du -h

du --max-depth=<depth> # print the total for a directory (or file, with --all) only if it is N or fewer levels below <depth>
# --max-depth=0 is the same as --summarize
# du -d

du --threshold=<size> # exclude entries smaller than SIZE if positive, or entries greater than SIZE if negative; du -t
```

```sh
du  --threshold=1MB --human-readable --all *
```

