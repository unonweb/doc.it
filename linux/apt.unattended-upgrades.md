# unattended upgrades

* https://wiki.debian.org/UnattendedUpgrades
* Canonical has opted to pre-install the `unattended-upgrades` package and enable it across the Ubuntu operating system

This program can download and install security upgrades automatically and unattended, taking care to only install packages from the configured APT source, and checking for dpkg prompts about configuration file changes. All operations are logged to /var/log/unattended-upgrades/unattended-upgrades.log and the package manager (dpkg) output is logged to /var/log/unattended-upgrades/unattended-up‐grades-dpkg.log

This script is the backend for the `APT::Periodic::Unattended-Upgrade` option and designed  to be run periodically by `apt-daily-upgrade.service

# PATHS

```sh
ll /var/lib/apt/periodic # here upgradable packages are stored
```

# INSTALL

```sh
apt list unattended-upgrades
sudo apt install unattended-upgrades

sudo apt install bsd-mailx # simple command-line-mode mail user agent; even if you don't use it, it may be required by other programs.
```

```sh
# Check that it's running:
sudo systemctl status unattended-upgrades.service
```

# CLI

```sh
sudo unattended-upgrades --dry-run --debug
# Run the manually:
unattended-upgrades --verbose
```

# config

Default: automatically retrieve **bug fix and security updates for most of the packages** included in the Ubuntu repositories.

```sh
sudo dpkg-reconfigure -plow unattended-upgrades
# Once completed, the app will create the file /etc/apt/apt.conf.d/20auto-upgrades
```

```sh
# Configure automatic unattended updates:
sudo xed /etc/apt/apt.conf.d/50unattended-upgrades
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades

# Debian:
sudo nano /etc/apt/apt.conf.d/52unattended-upgrades-local
```

```sh
Unattended-Upgrade::Mail "root";
```



```sh
sudo nano /etc/apt/apt.conf.d/20auto-upgrades

APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

# log

```sh
tail -e /var/log/dpkg.log 
ll /var/log/unattended-upgrades/

journalctl -u apt-daily.service | tail
```

# apt-listchanges

```sh
sudo apt install apt-listchanges # compare a new version of a package with the one currently installed and show what has been changed, by extracting the relevant entries from the Debian changelog
```

```sh
sudo nano /etc/apt/listchanges.conf

[apt]
frontend=pager
email_address=root
confirm=0
save_seen=/var/lib/apt/listchanges.db
which=both
```

# timer-units

```sh
sudo nano /lib/systemd/system/apt-daily.timer # Used for downloads, gets overridden by:
sudo nano /etc/systemd/system/apt-daily.timer.d/override.conf 

sudo nano /lib/systemd/system/apt-daily-upgrade.timer # Used for upgrades, gets overridden by:
sudo nano /etc/systemd/system/apt-daily-upgrade.timer.d/override.conf
```
  
# HOW TO

## add additional repository to unattended upgrades

- run `apt-cache policy`
- There you will find an entry like this:
```
  500 https://download.docker.com/linux/ubuntu focal/stable amd64 Packages  
     release o=Docker,a=focal,l=Docker CE,c=stable,b=amd64  
     origin download.docker.com 
```