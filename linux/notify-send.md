# notify-send

* program to send desktop notifications

```sh
notify-send [options] "title" "message"

notify-send -u
notify-send --urgency=<lvl> # specifies the urgency level (low, normal, critical).

notify-send -t
notify-send --expire-time=<s> # duration, in milliseconds, for the notification to appear on screen. (Ubuntu's Notify OSD and GNOME Shell both ignore this parameter.)

notify-send -i
notify-send --icon=<icon> # specifies an icon filename or stock icon to display.

notify-send -c
notify-send --category=<type> # specifies the notification category.
```
