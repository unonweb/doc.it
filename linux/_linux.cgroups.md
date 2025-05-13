
Cgroups organisieren Prozesse auf einem Linux-System in hierarchischen Baumstrukturen. Sie weisen einzelnen Bereichen der Hierarchie optional verschiedene Parameter zur Ressourcenverwaltung zu. Damit lassen sich etwa Speicherlimits für einen bestimmten Prozess oder eine Gruppe von Prozessen festlegen, die der Kernel durchsetzt.

```sh
cat /proc/$$/cgroup # get cgroup of shell

0::/user.slice/user-1000.slice/user@1000.service/app.slice/app-org.gnome.Terminal.slice/vte-spawn-12ed9380-2972-4c2b-843d-683d39022b48.scope
```