# ddrescue

data recovery tool

Copies data from one file or block device to another, trying to rescue the good parts first in case of read errors.
Always  use  a mapfile unless you know you won't need it. Without a mapfile, ddrescue can't resume a rescue, only reinitiate it.

```sh
sudo apt install gddrescue
```

```sh
ddrescue [options] infile outfile [mapfile]

ddrescue -B
ddrescue --binary-prefixes # show binary multipliers in numbers
ddrescue -f
ddrescue --force # overwrite output device or partition
# -f is necessary when you are outputting to a block device instead of a file
ddrescue -v
ddrescue --verbose
ddrescue -d
ddrescue --idirect # use direct disc access for input file
ddrescue -n
ddrescue --no-scrape # skip the scraping phase
ddrescue -R
ddrescue --reverse # reverse the direction of all passes
```

```sh
# mapfile
ddrescue /dev/sda recovery.img rescue.log
# Wenn der Datenrettungsprozess unterbrochen wird, können Sie ihn mit demselben Befehl fortsetzen. DDRescue verwendet die Logdatei, um herauszufinden, wo er aufgehört hat:
ddrescue /dev/sda recovery.img rescue.log
```

```sh
# partition > image
ddrescue -d -r3 /dev/sda test.img test.logfile
# -d use direct disk access and ignore the kernel’s cache
# -r3 retry bad sectors 3 times before giving up

Note: On a failing drive you may want to eliminate this option the first time so as to not waste time hammering on bad sectors and risking drive failure. You can always use the logfile to go back and retry the bad sectors after you get an image from the first sweep.
```

```sh
# image > partition
ddrescue -f test.img /dev/sda restore.logfile
# -f is necessary because you are outputting to a block device instead of a file
# do not use -d (is only applicable when the source is a block device, not a file)
```

```sh
# partition > partition
sudo ddrescue -d -f -r3 /dev/sda /dev/sdb /home/frida/rescue.logfile
sudo ddrescue -d -f /dev/sda /dev/sdb /home/frida/rescue.logfile
```



```sh
# 1. Clone partition without retrying to read bad blocks
sudo ddrescue --verbose --idirect --no-scrape --force /dev/sdc1 /dev/sdd1 ~/ddrescue_clone_sdc1-sdd1.log
# --no-scrape skip the scraping phase. Avoids spending a lot of time trying to rescue the most difficult parts of the data. In order to preserve the disk I skipped this phase at least for the first run. As every additional hard disk seek could cause even more damage to the disk I first tried to get every piece of data that is still readable. Trying to read the potentially damaged data will be done in a second run (see further down below).

# 2. If there were bad blocks (see log file), try to read them 2 times more:
sudo ddrescue --verbose --idirect -r3 --force /dev/sda1 /dev/sdb1 ~/ddrescue_clone_sda1-sdb1.log
```
