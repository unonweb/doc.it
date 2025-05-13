
# INITRAMFS

**initial ram filesystem** = Nachfolger von **inird** (initial ramdisk oer inital root disk)

Speicherort: `/boot/initrd.img-<kernelversion>`

```sh
mkinitramfs # low-level tool for generating an initramfs image
mkinitramfs -o <outfile> # write the image to *outfile*

dracut # low-level tool for generating an initramfs/initrd image; wird i.d.R. ohne Optionen aufgerufen
```

- ist ein i.d.R. mit **gzip** komprimiertes **cpio-Archiv**
- enthält für den Systemstart benötigte Dateien (auch Busybox); kann vom Linux-Kernel beim Booten als / eingehängt werden. Anschließend wird ein auf dem initramfs vorhandenes Programm (init) gestartet.
- Just like the initrd, it contains the tools and scripts needed to mount the file systems *before* the init binary on the real root file system is called. These tools can be decryption abstraction layers (for encrypted file systems), logical volume managers, software raid, etc.
- ist nur in folgenden Situationen zwingend erforderlich
  * If the root file system is encrypted, then the Linux kernel will not be able to find the init application, resulting in an unbootable system. 
    The solution for this problem has since long been to use an initrd (initial root device). 
  * When the /usr partition is on a separate file system, tools and drivers that have files stored within /usr cannot be used unless /usr is available. If those tools are needed to make /usr ailable, then we cannot boot up the system

### busybox

Werden beim Ablauf von init schwerwiegende Fehler entdeckt, startet BusyBox. Busybox ist ein Minibetriebssystem und stellt eine Shell zur Eingabe und Ausgabe bereit.

### lsinitramfs

```sh
lsinitramfs <options> <initramfsfile> # list content of an initramfs image
unmkinitramfs # extract content from an initramfs image
```

manually

```sh
cp /boot/initramfs /tmp/initramfs.gz
gunzip /tmp/initramfs.gz
mkdir /tmp/initramfs.dir
cd /tmp/initramfs.dir
cpio -i < /tmp/initramfs
```
