inxi
=========================================================

# system & summaries

```sh
inxi --basic # inxi -b 
# basic output, short form
inxi --system # system info: host name, kernel, desktop environment, distro, dm (or startx), taskbar, panel; inxi -S
inxi --machine [-xx] # machine data: Device, Motherboard, BIOS, System Builder (if present); inxi -M
```

# hardware classes

```sh
inxi --audio # audio/sound card info: card driver; inxi -A
inxi --graphics # graphic card info: card and card driver, display protocol, display server (vendor and version number): inxi -G
inxi --cpu # CPU info: per CPU clock speed and CPU max speed; inxi -C

# MT Multi/Hyper Threaded CPU, more than 1 thread per core (previously HT).
# MCM Multi Chip Model (more than 1 die per CPU).
# MCP Multi Core Processor (more than 1 core per CPU).
# SMP Symmetric Multi Processing (more than 1 physical CPU).
# UP Uni (single core) Processor.  

inxi --battery [-xxx] # battery information: charge, condition, plus extra information; inxi -B
inxi --memory # Memory (RAM) data, inxi -m
inxi --network [-x] # network card info: card driver; PCI BusID, Port number; inxi -N
inxi --disk [-xx] # hard disk info; inxi -D
inxi --disk-full [-xx] # like -D +optical drive info; inxi -d
inxi --slots # PCI slot info: type, speed, and status information
```

# file system

```sh
inxi --partitions-full # shows full partition info; inxi -p
inxi --label # like -P + labels; inxi -l
inxi --uuid # like -P + UUIDs; inxi -u
inxi --raid # shows RAID devices, states, levels, components extra data with -x / -xx; inxi -R
```

# output

```sh
inxi --output [json|screen|xml]
```

