lshw
==========================================================

list hardware; *memory configuration*, *firmware version*, *mainboard configuration*, *CPU version and speed*, *cache configuration*, *bus speed*, etc.  

* must be run as super user or it will only report partial information

```sh
lshw -short
lshw -businfo # zeigt eine kurze Liste der Hardware aller Klassen + Pfad/Businformation
```

```sh
sudo lshw -class <class> # only show the given class of hardware (class can be found using lshw -short|-businfo)
sudo lshw -class network
```

```sh
lshw -X # launches the X11 GUI (if available)
```

```sh
sudo lshw -html > ~/Desktop/hardwareprofile.html
```
