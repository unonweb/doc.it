# LINKS

- https://www.youtube.com/watch?v=7WbREHtc5sU

# KERNEL BOOT PARAMETERS

```sh
cat /proc/cmdline # enthält alle Parameter (Bootoptionen), die der Bootloader dem Kernel übergeben hat.
# BOOT_IMAGE=/vmlinuz-5.4.0-37-generic root=/dev/mapper/vg00-lv01 ro apparmor=0
```

## parameters

```sh
root=UUID=6170816b-050f-4da6-8ae2-cda1a7bd81cd # definiert das root-Device (root=UUID oder root=/dev/...)
nosplash / noplymouth # schaltet die graphische Animation während des Bootvorgangs ab
quiet # unterdrückt Log-Meldungen des Kernels
fsck.mode=force # erzwingt fsck bevor das Dateisystem eingehängt wird
1|2|3|5 # definiert das runlevel
ro # mount root file system in read-only mode, so, that fsck program can perform a file system scan
rw # mount root file system in read-write mode
Hdx # Adjust IDE driver Geometry; very handy if BIOS is generating irrelevant and incorrect Information.
init= # sets the initial command that needs to be executed by kernel. If ‘init‘ is not set, it searches for the ‘init‘ in the following respective locations before the kernel lands into panic mode: /sbin/init, /etc/init, /bin/init, /bin/sh
```

### systemd

```sh
systemd.unit=emergency.target # will provide you with a root shell at the earliest possible point. However, this is done before mounting the root file system with read-write permissions.
systemd.debug-shell=1 # enable the systemd “early debug shell”; opens a root login shell on tty9 very early in the boot process
```



## set

* permanently

  ```sh
  xed /etc/defaults/grub # unter GRUB_CMDLINE_LINUX=
  update-grub
  ```

* temporarily

  **Systemstart (einmalig): im Editor** (Taste e)

  Bootoptionen werden (durch ein Leerzeichen getrennt) an die Zeile angefügt, die mit *linux* ... oder *kernel* ... beginnt.

## how to

### reset Root PW

```sh
init=/bin/bash # wird als Bootoption an die Zeile angehängt, die mit linux startet; statt systemd/init wird die Shell (ohne Passwortabfrage) gestartet
mount -o remount,rw / # da / im read-only-Modus eingehängt wird, muss es neu gemountet werden
passwd root
```
