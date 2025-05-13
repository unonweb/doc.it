
# HOW TO

##### start GUI from cmd

```sh
# use alt+strg+f1 to enter cmdline from login screen
startx
```

# DISPLAY MANAGER

- eine grafische Anmeldung vor dem Starten einer Desktop-Umgebung 
- ggf. eine Auswahl der installierten Desktop-Umgebungen
- bereitet die Desktop-Umgebung vor und startet sie
- Lightdm, GDM, KDM

### LightDM

weniger komplex als GDM bei fast gleicher Funktionalität; Standard bei Debian

```sh
sudo start lightdm
 
cat /etc/X11/default-display-manager

/etc/lightdm/lightdm.conf
```

# X-Window

Entwickler: X.Org Foundation; 1987 X11; aktuell X11R7.7

**Client-Server-Applikation**; Kommunikation zwischen X-Server und X-Clients durchläuft immer die Netzwerkschicht

- **X-Server** steuert die Ein- und Ausgabegeräte, wie Maus, Tastatur und Bildschirm. Die Anwendungsprogramme stellen X-Clients dar; kommuniziert über den Kernel mit der Grafikhardware
- **X-Libraries** (Oberflächenbibliotheken)**:** GTK+ (GIMP Toolkit): GNOME // Qt: KDE

Will man sein System schmal halten; sollte man sich für eine der beiden Bibliotheken entscheiden.

```sh
~/.xsession-errors # Errorlogdatei
$DISPLAY # this variable gets set to the name of the display to which clients should connect; it gets set, not read!
```

### startx / xinit

**startx** ist ein Frontend für xinit um eine X-Session zu starten; it is often run with no arguments; sollte als normaler Benutzer ausgeführt werden, da die Datei .Xauthority sonst mit Root-Rechten angelegt wird und normale Benutzer dann nicht mehr darauf zugreifen können; greift auf folgende zwei Skripte zurück, um a) den Server und b) die Clients zu starten:

```sh
/etc/X11/xinit/xserverrc 
# Startskript für den X-Server (welcher Server wird mit welchen Optionen gestartet?)
# der X-Server sollte hier mit der Option -nolisten tcp gestartet werden (ein mit -listen tcp gestarteter X-Server ist aus dem Netzwerk/Internet erreichbar)

/etc/X11/xinit/xinitrc 
# Startskript für xinit; legt fest welche X-Clients gestartet und welcher Fenstermanager verwendet werden soll; wenn das Skript beendet ist, wird auch X beendet

/etc/X11/Xsession 
# used/executed by display managers when a user logs in (the display manager invokes the “custom" session type)
```

