# fwupdmgr

**firmware update manager client utility**

* intended to be used interactively

* https://github.com/fwupd/fwupd

* https://fwupd.org/ 

  Linux Vendor Firmware Service

  Site used by all major Linux distributions to provide metadata for clients such as `fwupdmgr` and GNOME Software

* 2018 **Lenovo** joined the project and provides update support for a wide range of their devices
* 2019 **Acer** joined the project

```sh
fwupdmgr get-devices # Listet alle erkannten Geräte auf.
fwupdmgr refresh # Aktualisiert über den Linux Vendor Firmware Service 🇬🇧 die Datenbank.
fwupdmgr get-updates # Anzeige verfügbarer Updates.
fwupdmgr update # Herunterladen der Firmware und Durchführung des gewünschten Updates. 
```



## fwupd

The fwupd daemon **allows session software to update device firmware on the local machine**.