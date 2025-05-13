## DKMS

**Dynamic Kernel Module Support**; framework which **allows kernel modules to be dynamically built for each kernel on your  system** in a simplified and organized fashion; von Dell entwickelt; kommt zum Einsatz, wenn für eine Software oder Hardware eigene Kernelmodule benötigt werden (z.B. Grafikkarten von Nvidia; Virtualbox); 

DKMS prüft beim Systemstart automatisch, ob eine neue Kernelversion installiert wurde und bindet die Module, die mittels DKMS installiert wurden, bei Bedarf in den neuen Kernel ein; ohne DKMS müssten diese Module bei jedem Kernelupgrade manuell kompiliert und wieder eingebunden werden.

Damit DKMS funktioniert, sind **Headerfiles** passend zum laufenden Kernel erforderlich und müssen ggf. heruntergeladen werden.

Neue Kernel-Serien erscheinen in der Regel bevor proprietäre Treiber sie über DKMS unterstützen. Wenn Sie proprietäre Treiber verwenden, empfiehlt es sich, Kernelaktualisierungen auszusetzen und keine Kernel aus Serien zu installieren, die neuer sind als die aus der Serie der empfohlenen Kernel.

```sh
dkms status # zeigt aktuell von DKMS überwachte Module
dkms install -m <module> -v <module.version> -k <kernel.version> 
# im Anschluss muss noch ein Setup-Skript des installierten Moduls geladen werden.


ll /lib/modules/(kernelversion)-generic/updates/dkms
```

## lsmod

**listet aktuell geladene Module** (die nach dem Start des Kernels zusätzlich geladen wurden); 
searches */proc/modules**;* gelistete Module können alle entladen werden

Spalte *used*: Abhängigkeiten zu anderen Modulen

## modprobe / kmod

**Add and remove modules** from the Linux Kernel; kann Abhängigkeiten auflösen; Informationen über Abhängigkeiten bezieht modprobe von depmod; funktionalität ersetzt `insmode` und `rmmod`; durchsucht */lib/modules/Kernelversion*

*/etc/modprobe.d/blacklist.conf* 
lists those modules which we don't want to be loaded

usb_host und usb-host = identisch!

modprobe *module* # läd *module*

```sh
modprobe -r
modprobe --remove <module> # entfernt *module* (und auch überflüssig gewordene andere)
modprobe -c
modprobe --showconfig # Dump out the effective configuration from the config directory and exit.

modprobe --show-depends # List the dependencies of a module (or alias), including the module itself. This produces a (possibly empty) set of module filenames, one per line, each starting with "insmod" and is typically used by distributions to determine which modules to include when generating initrd/initramfs images.

modprobe -v
modprobe --verbose #
```

## modinfo

zeigt Informationen über ein Modul

```sh
modinfo <module> # Informationen über module
# listet alle verfügbaren Module (nicht nur die geladenen) sowie Autor, Abhängigkeiten
```

## depmod

depedencies of modules; aktualisiert die Datenbank der Abhängigkeiten unter /lib/modules/*Kernelversion*/modules.dep

```sh
depmod -A
depmod --quick 
# scans to see if any modules are newer than the modules.dep file before any work is done:  if not, it silently exits rather than regenerating the files
```