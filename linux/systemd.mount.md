* https://www.freedesktop.org/software/systemd/man/249/systemd.mount.html

Der `systemd-fstab-generator` liest bei jedem Systemstart aus der **fstab** und schreibt die generierten Mount-Units temporär nach `/run/systemd/generator/`. Anschließend können diese wie jede andere Unit mit systemctl verwaltet werden.

* Der Name der Mount-Unit muss den Pfad des Einhängepunktes widerspiegeln

```ini
[Unit]
# Abschnitt wird im Artikel systemd/Units beschrieben

[Mount]
Type=nfs4
What=10.10.10.10:/nfs-share
Where=/mnt/nfs

[Install]
# Abschnitt wird im Artikel systemd/Units beschrieben
```

## troubleshooting

#### eine systemd mount-Unit lässt sich mit mount nicht wieder einhängen

Eine Mount-Unit lässt sich zwar mit dem Befehl 

```sh
umount /mount_point 
```

stoppen, ein 

```sh
mount /mount_unit_point #
```

ist jedoch nicht möglich, da mount nur die fstab nach Einträgen durchsucht.

In dem Fall muss die Unit wieder via systemctl gestartet werden:

```sh
systemctl start /mount_unit_point 
```
