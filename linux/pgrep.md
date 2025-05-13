# pgrep

```sh
pgrep strapi # 15091
pgrep -l
pregp --list-name # lists the process name as well
pgrep -u
pgrep --euid
pkill -u
pkill --euid # only matches processes whose effective user ID is listed
pgrep -c
pgrep --count
pkill -c
pkill --count # print (just) a count of matching processes
pgrep -i
pgrep --ignore-case
pkill -i
pkill --ignore-case # match processes case-insensitively

pgrep -u <user> | xargs kill # entspricht: pkill -u user
```
