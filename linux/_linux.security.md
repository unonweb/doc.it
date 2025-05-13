
- Apparmor: Programme einzeln beschränken.
- Container: Programme bündeln (Microservices) und beschränken

# LINKS

- https://liveoverflow.com/protect-linux-server-from-hackers/
- https://www.welivesecurity.com/2021/10/07/fontonlake-previously-unknown-malware-family-targeting-linux/
- Hardening Ubuntu. Systemd edition:
  https://github.com/konstruktoid/hardening/blob/master/README.adoc

# THREATS

- **Trojanized applications** – modified legitimate binaries that are adjusted to load further components, collect data, or conduct other malicious activities.
- **Backdoors** – user mode components serving as the main point of communication for its operators.
- **Rootkits** – kernel mode components that mostly hide and disguise their presence, assist with updates, or provide fallback backdoors.

# COMPARISON
## container vs sandbox

- container managers: Docker, LXC, nspawn. 
- **A container is a separate root filesystem**. The software runs in this new filesystem.
- Firejail and bubblewrap are security sandboxes.
## sandboxing vs MAC

- **sandbox** is very powerful when a program does not access the fs or other program too much. For instance, in a server or a GUI app where the interactions are well defined and therefore it is easy to define a generic sandbox. 
- **MAC** is mostly used for core programs that have more interactions like systemd, dbus, polkit, Gnome... See the concepts section in the readme.

# CONCEPT

## very high

- read-only: /usr/
- apparmor: core programs
- sandbox: services, programs with little interaction

## minimum

- sandbox: all applications that directly come from the devs (flatpak, appimage)

## immutable/read-only core os

- https://www.redhat.com/en/blog/immutability-silverblue
- https://www.onlogic.com/blog/how-to-build-a-read-only-linux-system/
- https://blog.cscholz.io/linux-systemhartung-durch-read-only-der-usr-partition/
- ReadonlyRoot
  https://wiki.debian.org/ReadonlyRoot
- Immutable → reprovisionable, anti-hysteresis: 
  https://blog.verbum.org/2020/08/22/immutable-%e2%86%92-reprovisionable-anti-hysteresis/
- Fitting Everything Together
  https://0pointer.net/blog/fitting-everything-together.html

> instead of talking about an "immutable" system that allows in place updates, it’d be more useful and accurate to say "image based".


### implementation

- dual partitions
- OSTree
  based on hardlinking with a "hidden" writable data store

## immutable /usr

- https://lwn.net/Articles/890219/
- https://0pointer.net/blog/running-an-container-off-the-host-usr.html

> the reason `/usr` read-only is _primarily_ to **enforce that user configuration is cleanly separate from the OS content** – which becomes particularly important when OS updates are automatic by default, as they are in Fedora CoreOS [^1]


If a package manager is updated to be able to automatically re-mount `/usr` writable around the actual package manager operations the system is generally more secure, as the semantics move closer to the immutable semantics of image-based systems. Of course, true image-based systems offer a much greater level of security, because they make modification of the OS impossible entirely (instead of just restricting it to a specific time window), but if you are stuck in a package-based world, I believe it's still a major improvement.

[^1]: https://blog.verbum.org/2020/08/22/immutable-%e2%86%92-reprovisionable-anti-hysteresis/
