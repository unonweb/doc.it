# dd

**disk dump**; dient zum bit-genauen Kopieren von Festplatten, Partitionen oder Dateien; GUI: *gdiskdump*; kann aus einem Datenträger (CD, DVD, USB-Stick) ein Image erzeugen

```sh
dd # ohne Optionen, liest dd vom stdin bzw. schreibt auf stdout
dd if=<file> # input file
dd of=<file> # output file

dd bs=<bytes> # block size; es werden Blöcke mit der Größe *bytes* gelesen und geschrieben (K,M,G)
dd if=/dev/sda of=/dev/sdb bs=4096 # bs to set the number of bytes to copy at a single time (4,096)

dd count=<blocks> # gibt an, wie viele Blöcke mit der durch bs festgelegten Größe gelesen/geschrieben werden
dd seek=<size>[<unit>] # skip <size> before writing
dd count=1 bs=1 seek=10G # überspringt 10G und schreibt dann 1 Byte; d.h. effektiv wird eine 10G große Datei angelegt

dd if=/dev/sdX of=/dev/sdY bs=64 # By default, dd can take a while to transfer data. You can speed up the process by increasing the block size. Do so by adding bs= at the end.

# erstelle ein Backup des Bootloaders (der auf den ersten 446 Bytes von sda liegt):
dd if=/dev/sda of=bootloader1stagebackup bs=1 count=446 

# erstelle eine 100Mb große Datei voller Nullen:
dd if=/dev/zero of=file count=100 bs=1M
```

## clone drives with dd

dd can create an exact copy of your entire hard drive or partition. When you restore this copy, you bring your computer back to exactly how it was. You will get back your files, your apps, your browsing history, and even your system settings. Everything.

You can, of course, use `tar` or even `scp` to  replicate entire filesystems by copying the files from one computer and then pasting them as-is on top of a fresh Linux install on another computer. But, because those filesystem archives aren't complete images, they'll require a running host OS at both ends to serve as a base.

To clone your drive, you will need a second drive that has more space than the one you're copying.

```sh
# create an image:
dd if=/dev/sdX of=path/to/your-backup.img
# compress:
dd if=/dev/sdX | gzip -c > path/to/your-backup.img.gz # To save space, you can have dd compress your backup.  

# restore an image:
dd if=path/to/your-backup.img of=/dev/sdX 
# decompress:
gunzip -c /path/to/your-backup.img.gz | dd of=/dev/sdX

# backup an entire drive
dd if=/dev/sda of=/dev/sdb
# backup one partition
dd if=/dev/sda2 of=/home/username/partition2.img bs=4096
```

## wipe drives

```sh
dd if=/dev/zero of=/dev/sda1
dd if=/dev/urandom of=/dev/sda1
```

"*dd* stands for *disk destroyer*"