### info

```sh
dpkg-query -L <pgk> # shows all files belonging to pkg
```

### required

```sh
ubuntu-system-adjustments.service # this pkg modifies some ubuntu unwanted behavior and is needed for Mint. do not disable or remove
rtkit-daemon.service # sounds scary, like rootkit, but you need it because it is the real-time kernel scheduler.
wpa_supplicant.service # is necessary only if you use a Wi-Fi network interface.
```

### disable!

```sh
sudo systemctl disable --now touchegg.service # gestures
sudo systemctl disable --now bluetooth.service
sudo systemctl disable --now debug-shell.service # opens a giant security hole and should never be enabled except when you are using it. This provides a password-less root shell to help with debugging systemd problems.
sudo systemctl disable --now brltty.service # provides Braille device support
sudo systemctl disable --now ModemManager.service

sudo systemctl disable --now pppd-dns.service # is a relic of the dim past. If you use dial-up Internet, keep it. Otherwise, you don’t need it.
sudo systemctl disable --now whoopsie.service # is the Ubuntu error reporting service. It collects crash reports and sends them to https://daisy.ubuntu.com. You may safely disable it, or you can remove it permanently by uninstalling apport.
```

# SERVICES

## accounts-daemon.service

- https://www.freedesktop.org/wiki/Software/AccountsService/
- package: accountsservice
- provides a set of D-Bus interfaces for querying and manipulating user account information
- provides an implementation of these interfaces, based on the useradd, usermod and userdel commands.
- may or may not be used by the display manager (login screen), the screensaver, and the account management tools in your desktop environment.

```sh
sudo systemctl status accounts-daemon.service
```

## avahi-daemon.service

```sh
sudo systemctl disable --now avahi-daemon.service # is supposed to provide zero-configuration network discovery, and make it super-easy to find printers and other hosts on your network. I always disable it and don’t miss it.
```



## blueman-mechanism.service

* https://github.com/blueman-project/blueman/tree/main
* Blueman is a GTK+ Bluetooth Manager

## bluetooth.service

```sh
sudo systemctl status bluetooth.service
sudo systemctl disable --now bluetooth.service
```
## colord.service

is a systemd service that manages color profiles and color management on Linux systems. It is part of the Colord project, which provides a centralized color management solution for Linux desktops.

## e2scrub_reap.service

is a systemd service associated with the e2scrub utility, which is part of the e2fsprogs package. This service is designed to perform filesystem scrubbing on ext2, ext3, and ext4 filesystems.

## gnome-remote-desktop.service

s a systemd service associated with the GNOME Remote Desktop feature, which allows users to remotely access and control a GNOME desktop environment. This service enables remote access using protocols such as VNC (Virtual Network Computing) or RDP (Remote Desktop Protocol), facilitating remote desktop sharing and management.

## ModemManager.service

- a DBus-activated daemon
- controls **mobile broadband interfaces** (2G/3G/4G)

If you don’t have a mobile broadband interface — built-in, paired with a mobile phone via Bluetooth, or USB dongle — you don’t need this.

```sh
sudo systemctl disable --now ModemManager.service
```

## rtkit-daemon.service 

**real-time kernel scheduler**

## switcheroo-control.service

is a systemd service that manages GPU switching on systems with hybrid graphics configurations, such as those using both integrated Intel graphics and discrete NVIDIA or AMD GPUs. This service is part of the Switcheroo Control framework, which allows for dynamic switching between different graphics processors.

## thermald.service

is a systemd service associated with the thermald daemon, which is designed to monitor and manage the thermal conditions of a Linux system. Its primary role is to prevent overheating by regulating the temperature of the CPU and other components.

## xrdp.service

is a systemd service that enables the xrdp server on Linux systems, allowing users to connect to the desktop environment of a Linux machine using the Remote Desktop Protocol (RDP). This service is commonly used for remote desktop access, making it easier for users to interact with a Linux desktop from a Windows machine or any other RDP client.

## zfs-load-module.service

is a systemd service used to load the ZFS (Zettabyte File System) kernel module at boot time on Linux systems. ZFS is a powerful file system and volume manager that offers advanced features such as snapshots, data integrity, and storage pooling.