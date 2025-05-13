Bootstrap a basic Debian system

```bash
debootstrap [OPTION...]  SUITE TARGET [MIRROR [SCRIPT]]
```

bootstraps a basic Debian system of `SUITE` into `TARGET` from `MIRROR` by running `SCRIPT`.  

- MIRROR can be
    - `https://` 
        - http://ftp.de.debian.org/debian/
    - `file:///` 
    - `ssh:///`
- SUITE may be
    - a release code name: sid, stretch, jessie
    - a symbolic name: unstable, testing, stable, oldstable

```bash
sudo debootstrap --include=systemd,systemd-container,dbus stable /var/lib/machines/debian https://mirror.hs-esslingen.de/debian
```

```bash
sudo debootstrap --arch=ARCH # Set the target architecture (use if dpkg isn't installed). See also --foreign.
sudo debootstrap --include=<pkg1, pkg2> # Comma separated list of packages which will be added to download and extract lists.
```