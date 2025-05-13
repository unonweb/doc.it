# upower

Abstraktionsschicht zur Energieverwaltung für Linux

```sh
upower -i /org/freedesktop/Upower/devices/battery_BAT0
upower -e
upower --enumerate # enumerate objects paths for devices
```



**UPower** system-wide power management

**upowerd** provides the org.freedesktop.UPower service on the system message bus; this daemon is automatically started by dbus-daemon whenever an application calls into the service mentioned above