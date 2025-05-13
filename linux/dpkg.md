(Debian Package)

```sh
dpkg --install 'dateiname.deb' # installiert ein lokal vorliegendes .deb-Paket; dpkg -i
```

# dpkb-query actions

```sh
dpkg --list # listet alle installierten Pakete; dpkg -l
# Alternative: apt list --installed
```

```sh
dpkg --list | egrep -i --color 'linux-image|linux-headers' # shows a list of installed kernels with the following flags:
# ii - Marked for installation (i) and currently installed (i). Packages with these flags are safe to remove.
# rc - Removed (r) and configuration files present (c). The package is removed, and configuration files require purging.
# iU - Marked for installation (i) and unpacked (U) to install at the next reboot. Do not remove packages with this flag.

# Do not remove any packages that have the current kernel version!
uname -r # check the current kernel version
```

```sh
dpkg --listfiles <pkg> # Welche Dateien gehören zum Packet? dpkg -L
dpkg --search <file> # Zu welchem installierten Paket gehört die Datei? dpkg -S
dpkg --status <pkg> # Berichte den Status des spezifizierten Pakets. dpkg -s
```

# log files

```sh
less /var/log/dpkg.log # primary log file for dpkg
# For older entries, you may find compressed logs like dpkg.log.1, dpkg.log.2.gz, etc.
```

## reinstall packages based on the `dpkg` log files

```sh
grep 'install ' /var/log/dpkg.log
zgrep 'install ' /var/log/dpkg.log.*.gz # For compressed logs
grep 'install ' /var/log/dpkg.log | awk '{print $4}' | sort -u > installed-packages.txt # Create a List of Packages
xargs -a installed-packages.txt sudo apt-get install # Reinstall Packages
```

# dpkg-buildpackage

```sh
dpkg-buildpackage -b 
dpkg-buildpackage --build="binary"

dpkg-buildpackage -d
dpkg-buildpackage --no-check-builddeps # Do not check build dependencies and conflicts
```