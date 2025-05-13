# caja

* https://wiki.ubuntuusers.de/Caja/

## remote filesystems

```sh
ssh://benutzer@server:port/pfad/ordner 
```

```sh
sftp://benutzer:passwort@server:port # Praktisch identisch zu ssh://
```

```sh
davs://login:passwort@example.com # Identisch zu dav://, allerdings wird die Verbindung verschlüsselt.
```

```sh
ftp://192.168.0.1
```

## environment variables

```sh
$CAJA_SCRIPT_CURRENT_URI # URI for current location
$CAJA_SCRIPT_SELECTED_FILE_PATHS # newline-delimited paths for selected files (only if local)
$CAJA_SCRIPT_SELECTED_URIS # newline-delimited URIs for selected files
$CAJA_SCRIPT_WINDOW_GEOMETRY # position and size of current window
```



## scripts

```sh
ll ~/.config/caja/scripts
```

```bash
# Mit wipe sicher löschen: 
#!/bin/bash
for FILE in "$@"; do
    /usr/bin/wipe -rf "${FILE}"
done
```

```sh
#!/bin/bash

# Created by oss-lvr (https://github.com/oss-lvr)
# Open a terminal from anywhere

mate-terminal $CAJA_SCRIPT_CURRENT_URI
```

```sh
chmod u+x wipe.sh
```



Caja ermöglicht – analog zu Nautilus – die Verwendung von Skripten als Erweiterung im Kontextmenü. Diese Skripte sind ausführbare Dateien, welche im Verzeichnis `~/.config/caja/scripts/` gespeichert werden. Damit neu installierte Erweiterungen verfügbar sind, muss Caja neu gestartet werden. Sie sind über den Kontextmenüeintrag "Skripte" aufrufbar, der Name des Menüeintrages entspricht dabei dem Namen des Skriptes.