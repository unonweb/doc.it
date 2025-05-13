# BOOT ORDER

## BIOS

Basic Input/Output System

1. Power On Self Test (POST): Initialisierung der Hardware (erkennen und ansprechbar machen)
2. Bootloader im MBR suchen und in den RAM laden
3. Kontrolle an den Bootloader abgeben

## MBR/Grub 1st stage

Spur 0, Sektor 0 (512 bytes); der MBR selbst liegt auf keiner Partition, sondern ist eigenständig

       1. 1st stage Grub: Verweis auf die Partition, wo der eigentliche bootloader (2nd stage liegt)
       2. falls kein Bootloader vorhanden ist, wird die Partitionstabelle konsultiert 

## Grub 2nd stage

`/boot/grub`
`/boot/grub/grub.conf` laden und die eingetragenen Betriebssysteme zur Auswahl anbieten

Der Bootloader hat folgende Aufgabem:

1. Bootoptionen an den Kernel übergeben
2. Den Kernel (/boot/vmlinuz-Kernelversion) in den RAM laden und starten
3. Das Initramfs-Archiv in den RAM laden

## Kernel

`/boot/vmlinuz-<kernelversion>`

**vmlinuz** is the name of the Linux kernel executable; it's compressed and it is bootable.

1. prüfen, ob ein initramfs-Archiv vorhanden ist; ggf. dekomprimieren und als / einhängen oder das reale / direkt einhängen
2. `/init` selbst oder aus dem initramfs starten

### iniramfs/initrd

1. entschlüsseln von /; LVM
2. mount the real root file system
3. `/sbin/init` starten 

### /root

## systemd

`/sbin/init` > `/lib/systemd/systemd`

* Einträge aus der `/etc/fstab` einhängen
* Dienste aus dem `default.target` starten (/lib/systemd/system/default.target)

# UEFI or BIOS?

```sh
[ -d /sys/firmware/efi ] && echo UEFI || echo BIOS # show if you've got BIOS or UEFI
```

# BIOS update

## Q-Flash

Q-Flash is a BIOS flash utility embedded in Flash ROM. With Q-Flash you  can update the system BIOS without having to enter operating systems like MS-DOS or Windows first. (Not all motherboards support Q-Flash™, please use @BIOS™ or DOS flash  utility to update BIOS if your motherboard does not have Q-Flash™.)

### instructions

1. According to your motherboard model, download the latest compressed BIOS update file from Gigabyte’s official website.
2. Extract the BIOS update file and save it to your USB flash drive or hard drive.
   The USB drive or the hard drive must be formatted with FAT32/16/12 file system
3. Plug the USB drive containing the BIOS update file into the computer.
4. Restart the computer and press the **End** key to enter **Q-Flash** during the boot-up process.
   Alternatively, you can also press F8 in BIOS Setup to access Q-Flash.
5. After the update process is complete, you will see the “**BIOS update is completed. Power off/Reboot**” message. 
   Here, pick **Reboot** to restart the computer. Press the **Delete** key during the POST to enter BIOS setup.
6. Choose **Load Optimized Defaults** on the **Save & Exit** screen and press **Enter** to load BIOS defaults.
   Then system will re-detect all peripheral devices after a BIOS update.

Having Q-Flash available, you can just download the BIOS update and copy the BIOS image (e.g. `AB350NGW.51d` in my case) to `/bin/efi`, which is the EFI partition readable by the BIOS. Next, just reboot into the BIOS and point the Q-Flash utility to that file.

## post-update quirks

Unfortunately, the BIOS update overwrites some important settings.  Most notably your Machine Owner Keys (MOK) that are required for Secure  Boot are lost.

So after update, change the following settings

- CSM Support: *disabled*
- Secure Boot: *enabled*
- Secure Boot Mode: *standard*

After this you have to re-enroll your MOK again. The easiest way is to re-install the `nvidia-dkms-XXX` package which triggers the process.

Upon reboot you will be greeted by the EFI menu, where you should  select “Enroll MOK” and and enter the code that you chose previously.

You can verify that everything went right by rebooting and running

```
mokutil --sb-state
```

Also, if you want to Docker or any kind of Virtualisation, you have  to re-enable AMD Secure Virtual Machine (SVM), which is inconveniently  buried under

- M.I.T > Advanced Frequency Settings > Advanced CPU Settings > SVM Mode: *enabled*

# (U)EFI

EFI kann man als eigenständiges, zwischengeschaltetes Betriebssystem verstehen, das umfangreiche Aufgaben übernehmen kann und weitere Komponenten in einem eigenen Dateisystem innerhalb des Ordners /EFI vorhält. Auch auf UEFI-Systemen ist weiterhin ein BIOS im Einsatz.

UEFI kann auf einer MBR-Platte durchgeführt werden.

Bootloader kann integriert sein.


## ESP (EFI System Partition)

mit FAT32/vFAT formatiert; unter `/boot/efi` eingehängt; i.d.R. 200MB groß; enthält first stage bootloader

### secure boot

nur vorher (und i.d.R. durch Microsoft) signierte Booloader mit der richtigen Prüfsumme werden zugelassen; MS bietet dafür einen Signaturdienst an; zur Authentifizierung muss dem UEFI der öffentliche Schlüssel/ das Zertifikat vorliegen; soll Bootsektorviren stoppen;

#### [Secure Boot & Windows]

Microsoft verlangt, dass Computer mit Windows-Logo mit aktiviertem Secure Boot ausgeliefert werden. Im Auslieferungszustand starten Secure-Boot-Systeme nur Bootloader, deren Code von Microsoft digital signiert wurde.

#### [Secure Boot & Linux]

- falls deaktiviert, kann die unsignierte Binärdatei grubx64.efi verwendet werden

- falls aktiviert, muss die Datei grubx64.efi mit einem Schlüssel versehen und in shim.efi umbenannt werden; danach ist sie durch den Bootloader Shim verwendbar.

Zur Umsetzung von Secure Boot anhand des Signierdienstes von Microsoft wird typischerweise der Bootloader **Shim** eingesetzt. Bei Shim handelt es sich um einen einfach strukturierten Open-Source-Bootloader (first stage), der von versch. Distributoren in Kooperation entwickelt wurde. Er ermöglicht es, Bootloader, die nicht von Microsoft signiert sind, indirekt zu starten. Shim kann somit als Bindeglied zwischen der von Microsoft geprägten Secure-Boot-Umgebung und Betriebssystemen Dritter eingesetzt werden.

Shim überprüft die Signatur des als nächstes zu ladenden Bootloaders. Das Schlüsselmaterial für diese Verifizierung kann hierbei aus drei Quellen stammen: 

- UEFI-Zertifikatsspeicher db und dbx
- Machine Owner Key List (MokList), ein Shim-eigener Zertifikatsspeicher
- im Shim-Binary hinterlegtes Zertifikat oder hinterlegter Hash

Ist die Verifizierung des zweiten Bootloaders, typischerweise Grub2, erfolgreich, so wird dieser ausgeführt. Schlägt die Verifizierung dagegen fehl oder kann der zweite Bootloader aus einem anderen Grund nicht geladen werden, so wird die zu Shim gehörende Anwendung **MokManager** aufgerufen, sofern diese installiert ist. Der MokManager ermöglicht es dem Anwender, interaktiv Zertifikate oder Hashes in die MokList einzufügen und somit die Ausführung des zweiten Bootloaders zu erlauben.

# **OTHER** **BOOTLOADER**

- **syslinux** (heute): boot from USB with FAT file system
- **isolinux**: boot from CD/DVD with ISO-9660 file system
- **isohybrid**: boot from usb with ISO-9660 file system
- **extlinux**: boot from Linux with ext file system

# HOW TO

* https://www.linuxbabe.com/command-line/how-to-use-linux-efibootmgr-examples

## change boot order

```sh
efibootmgr # show current boot order
sudo efibootmgr --bootorder XXXX,YYYY,ZZZZ # change boot order
```

## check

```bash
sudo dmidecode -t bios # prints a summary of SMBIOS data from sysfs
sudo inxi --machine -xxx
```
