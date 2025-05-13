lspci
=================================

Informationen über den PCI-Bus; durchsucht /proc/bus/pci/devices und die Datenbank /usr/share/misc/pci.ids
erste Spalte (Busnr. | Sockelnr. auf dem Bus)   
enp0se (ethernet | bus 0 | sockel 3)  

```sh
lspci -k # show kernel modules handling each device and modules capable of handling it.  
lspci -s <busnr>:<devnr> # shows only devices with specified devnr and/or busnr; muss am Ende stehen  
lspci -vvvs #  very very verbose; busnr:devnr schränken den Output ein; kann vorher mit lspci erfragt werden;  
```

```sh
lspci -v | grep xhci
```
