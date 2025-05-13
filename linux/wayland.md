# LINKS

- https://wiki.debian.org/Wayland
- https://wiki.ubuntuusers.de/Wayland/

# INFO

## check display protocol

```bash
echo $XDG_SESSION_TYPE 
# or:
echo $WAYLAND_DISPLAY
```

## Xwayland

Wayland bringt standardmäßig mit Xwayland auch eine Implementierung eines XServers mit. 

**Der Wayland Compositor startet Programme, die nicht mit Wayland zusammenarbeiten können, automatisch in einer X-Session unter Verwendung von Xwayland.** 

Dies geschieht z.B., wenn man – unsicher arbeitend! – mittels sudo eine grafische Anwendung mit Root-Rechten startet. Unter Wayland selbst wäre dies nicht möglich, da dies einen Benutzerwechsel in der laufenden Sitzung bedeutet, was die Sicherheitsrichtlinien des Wayland Protokolls nicht zulassen.

```bash
xlsclients # Um zu sehen, welche Programme in einer X Session laufen
```
## wayland compositor

Für die Umsetzung des Wayland-Protokolls ist ein Wayland Compositor zuständig bzw. notwendig. Dieser kümmert sich um die Darstellung des Fensterinhalts und der Fensterdekoration. 

- **Weston**: Referenzimplementierung des Wayland-Projekts
- **Mutter**: Fenstermanager des GNOME-Projekts
- **KWin**: Fenstermanager des KDE-Projeks