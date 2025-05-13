# id

* zeigt Gruppenzugehörigkeit etc.
* die Gruppen sind auch in `$GROUPS` enthalten
* the active set of groups is only determined at login
* if user is added to group, this is only reflected by *id* after logout / login

```sh
id # print real and effective user and group IDs
# uid=1000(frida) 
# gid=1000(frida)
# groups=1000(frida),4(adm),24(cdrom),27(sudo),29(audio),30(dip),46(plugdev),115(lpadmin),135(sambashare),136(vboxusers)

id -u | --user # print only the effective user ID
id -r | --real # print the real ID instead of the effective ID
```
