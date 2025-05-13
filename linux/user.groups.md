# groups

```sh
groups # shows groups of current user/process
groups <user> # shows <user>'s groups
cat /etc/group # show all groups
cat /etc/group | grep sudo # show all users of sudo group
# sudo:x:27:frida
# :x: represents password field
# :27: is the group id aka GID
```
