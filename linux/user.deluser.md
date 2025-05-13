# deluser / delgroup

Standardmäßig wird deluser den Benutzer entfernen, ohne das Home-Verzeichnis, den E-Mail-Puffer oder sonstige im System vorhandene Dateien zu löschen, die dem Benutzer gehören.

## remove user/group

```sh
deluser --remove-home <user> 
# entfernt Home-Verzeichnis, E-Mail-Puffer
deluser --remove-all-files <user> 
# entfernt alle Dateien des Benutzers aus dem System
deluser --backup-to <path> <user> 
# das Home-Verzeichnis und der E-Mail-Puffer des Benutzers werden unter path gesichert
delgroup --only-if-empty <group> 
# die Gruppe wird nur entfernt, wenn sie keine Mitglieder hat
```

## remove user from group

```sh
deluser <user> <group> # entfernt user aus group
sudo deluser frida ursula # info: Removing user `frida' from group `ursula' ...
```
