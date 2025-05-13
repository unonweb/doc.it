# su

**super user** und **switch user**

allows commands to be run with a substitute user and group ID

```sh
su # run an interactive shell as root
su <user> # switch to <user>; normale Benutzer werden nach dem Passwort von <user> gefragt; root nicht

su - 
su -l
su --login # 
# starts the shell as a login shell with an environment similar to a real login:
# clears most environment variables
# inits HOME, SHELL, USER, LOGNAME, PATH
# cd to the target user’s home

su user2 # verbleibt im aktuellen Verzeichnis von user1
su - user2 # führt zum Home Verzeichnis von user2
```

`su` is a tool for changing user identities and very few other process credentials temporarily. It's not a tool for opening a completely new login session. A new login session has a very well defined, pristine setup, not inheriting anything from any other session, but this is really not the case for `su` uid changes