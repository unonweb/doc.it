# chown

**change owner and group**

```sh
less /etc/group # zeigt Benutzer Gruppenzugehörigkeiten

chown user:group <file> # Trennungszeichen: user:group oder user.group
chown -R
chown --recursive # operate on files and directories recursively  

chown -R www-data:www-data /var/www # ändert Eigentümer und Gruppe von /var/www und allen Unterordnern auf Besitzer und Gruppe www-data
```

## files vs directories

```sh
find /path/to/base/dir -type d -exec chmod 755 {} + # recursively give DRECTORIES read & execute privileges (dirs only)
find /path/to/base/dir -type f -exec chmod 644 {} + # recursively give FILES read privileges (files only)
```

## relativ vs. absolut

Eine neue Datei hat aufgrund der umask 0022 i.d.R. die Berechtigung 0644. 
Sie soll für jeden Benutzer ausführbar gemacht werden. Alle der folgende Lösungen führen zum Ziel:

```sh
chmod 0755
chmod u=rwx,g=rx,o=rx
chmod a+x
chmod u+x,g+x,o+x
chmod g+s # adds the SGID-Bit
chmod u+s # adds the SUID-Bit
```

## links

`chown`, by default it changes the *target* of the symbolic link

If you'd like to change ownership of the link itself, you need to use the `-h` option to `chown`:

```sh
chown --no-dereference #  affect each symbolic link instead of any referenced file (useful only on systems that can change the ownership of a symlink); -h
```

