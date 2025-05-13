## hdparm

**get/set SATA/IDE device parameters**

```sh
sudo hdparm -I /dev/sda # Request identification info directly from the drive
sudo hdparm -I /dev/sda | grep -i speed
```

### benchmark

Perform timings of device reads for benchmark and comparison purposes. For meaningful results, this operation should be repeated 2–3 times on an otherwise inactive system (no other active processes) with at least a couple of megabytes of free memory. 

```sh
sudo hdparm -t /dev/sda # This displays the speed of reading through the buffer cache to the disk without any prior caching of data. This measurement is an indication of how fast the drive can sustain sequential data reads under Linux, without any filesystem overhead.
sudo hdparm -T /dev/sda # This displays the speed of reading directly from the Linux buffer cache without disk access. This measurement is essentially an indication of the throughput of the processor, cache, and memory of the system under test.

for i in 1 2 3; do sudo hdparm -tT /dev/sda; done
```
