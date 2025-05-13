
# GRUB

2012 Grub2 (komplette Überarbeitung)

- grub 1 fängt beim Durchnummerieren der Festplatten bei 0 an
- grub 2 fängt beim Durchnummerieren der Festplatten bei 1 an
- `/boot/grub/i386-pc/` # enthält Module, die von Grub geladen werden
- https://www.baeldung.com/linux/grub-bootloader-add-new-os

## mechanism

- Grub2 installs a loader into `/boot/EFI` partition & keeps its configuration in `/boot`
  (Grub2 is available as an UEFI payload)
- Computer turns on --> loads EFI --> loads GRUB2 from that EFI partition --> loads my OS
# ORDER

## BIOS

Die Stage 2 kann sich auf einer beliebigen Partition befinden. Unter Unix-Systemen befindet sie sich meistens unter /boot/grub/; enthält die Dateisystemtreiber, den Programmcode für das Auswahlmenü und die GRUB-Kommandozeile sowie die Laderoutine für die Kernel.

## UEFI

Startup on systems using UEFI firmware (common on motherboards since ca. 2012)

`/efi/<distro>/grubx64.efi` # is installed as a file in the EFI System Partition, and booted by the firmware directly, without a *boot.img* in sector 0.

/boot/grub/ # can be installed on the EFI System Partition as well.

# CONFIG

While `/etc/grub2.cfg` and the files in `/etc/grub.d` are reserved for automated updates, the file `/etc/default/grub` is for user customizations.

```sh
grub-mkconfig # generiert grub.cfg
update-grub # is a stub for grub-mkconfig -o /boot/grub/grub.cfg to generate a grub2 config file
```

## user settings

```sh
info -f grub -n 'Simple configuration' # info
```

```sh
xed /etc/default/grub # user config file
ll /etc/default/grub.d/ # user config files
```

Save and close the file. You've only changed a template file, not the  actual data that GRUB uses when your computer boots. To integrate your  change into the boot process, you must rebuild the active GRUB  configuration using the `grub2-mkconfig` command. The path you use for output depends on your computer's firmware structure.

```sh
GRUB_DEFAULT=0 # The default menu entry, identifies the Nth entry in the generated menu counted from zero,
GRUB_DEFAULT=<id> # useful if you want to set a menu entry as the default even though there may be a variable number of entries before it
GRUB_DEFAULT=saved # saved by *GRUB_SAVEDEFAULT* or *grub-set-default*

GRUB_CMDLINE_LINUX="" # Command-line arguments to add to menu entries for the Linux kernel.
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash" # two menu entries will be generated for each Linux kernel: one default entry and one entry for recovery mode.  This option lists command-line arguments to add only to the default menu entry, after those listed in *GRUB_CMDLINE_LINUX* (unless GRUB_DISABLE_RECOVERY is set to true)

GRUB_TIMEOUT=<seconds> # Boot the default entry this many seconds after the menu is displayed, unless a key is pressed.  The default is '5'.  Set to '0' to boot immediately without displaying the menu, or to '-1' to wait indefinitely.

GRUB_TIMEOUT_STYLE=menu # display the menu and then wait for the timeout set by *GRUB_TIMEOUT* to expire before booting the default entry.  Pressing a key interrupts the timeout. 
GRUB_TIMEOUT_STYLE=countdown # before displaying the menu, GRUB will wait for the timeout set by to expire. If <ESC> is pressed during that time, it will display the menu and wait for input.  If a hotkey associated with a menu entry is pressed, it will boot the associated menu entry immediately. If the timeout expires before either of these happens, it will boot the default entry.  In the *countdown* case, it will show a one-line indication of the remaining time.
```

## scripts

```sh
ll /etc/grub.d/ # Skripte, die die Menü-Einträge zur Auswahl des zu startenden Betriebssystems und auch das genaue grafische Erscheinungsbild konfigurieren

xed /etc/grub.d/10_linux # Identifies kernels on the root device for the operating system in use and creates menu entries for these items.

xed /etc/grub.d/30_os-prober # This script uses os-prober to search for Linux and other operating systems and places the results in the GRUB 2 menu
```

The scripts in this directory are read during execution of the `update-grub` and their instructions are incorporated into `/boot/grub/grub.cfg`

# GRUB SHELL

**Systemstart: Taste** `c`; einmalige Konfiguration

* **hd0,1** = /dev/sda1
* **hd1,1** = /dev/sdb1
* **hd3,2** = /dev/sdd2

## manual boot

```sh
Grup> ls # figure out root filesystem

Grup> ls (hd0,*n*) # n = root partition

Grup> set root=(hd0, 1) # sets the partition that the root filesystem is on

Grub> linux /vmlinuz root=/dev/sda1 ro systemd.unit=multi-user.target # sets the location of the kernel and sets the location of the root filesystem 

Grub> initrd /initrd.img oder initrd /boot/initrd.img-*version* # Pfad zur Ramdisk

Grub> boot
```
