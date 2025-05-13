# LINKS

- https://www.freedesktop.org/software/systemd/man/latest/systemd-nspawn.html
- https://www.linux-magazin.de/ausgaben/2016/02/systemd-nspawn/
- https://www.linux-magazin.de/ausgaben/2021/11/nspawnd-portabled
- https://wiki.debian.org/nspawn
- https://wiki.arcoslab.org/tutorials/tutorials/systemd-nspawn
- https://wiki.archlinux.org/title/Systemd-nspawn
- https://ramsdenj.com/posts/2016-09-22-containerizing-graphical-applications-on-linux-with-systemd-nspawn/
- Running a Container off the Host /usr/
  https://0pointer.net/blog/running-an-container-off-the-host-usr.html
- **run a command or OS in a light-weight container**
- einfache Container mit Kernel-Bordmitteln ohne allzu viel Lametta
- similar to LXC, but much simpler to configure

# PATHS

```sh
ll /etc/systemd/nspawn/machine.nspawn
ll /run/systemd/nspawn/machine.nspawn
ll /var/lib/machines/machine.nspawn
```

# INSTALL

```sh
sudo apt install systemd-container
```

# CLI

## examples

```bash
systemd-nspawn --directory=/var/lib/container/sid --setenv=DISPLAY=:0 --user=frida chromium
```

For use with untrusted programs, try this command instead:

```bash
systemd-nspawn --directory=/var/lib/container/sid --setenv=DISPLAY=:0 --user=frida --drop-capabilities=CAP_SYS_ADMIN chromium
```

### run vlc in vm

```bash
sudo systemd-nspawn --bind /tmp/.X11-unix:/tmp/.X11-unix \
--bind=/dev/shm \
--bind=/dev/dri \
--bind=/run/user/${UID}/pulse \
--bind=/etc/machine-id \
-directory /altroot/ARCH-ROOT \
su vlc -c "DISPLAY=:0 QT_X11_NO_MITSHM=1 dbus-launch /usr/bin/vlc --no-qt-privacy-ask"
```
## options

```bash
sudo systemd-nspawn -D
sudo systemd-nspawn --directory=<directory> # Directory to use as file system root for the container.
sudo systemd-nspawn --machine=<machine> # Sets the machine name for this container. This name may be used to identify this container during its runtime (for example in tools like machinectl(1) and similar), and is used to initialize the container's hostname (which the container can choose to override, however). If not specified, the last component of the root directory path of the container is used, possibly suffixed with a random identifier in case --ephemeral mode is selected. If the root directory selected is the host's root directory the host's hostname is used as default instead.
```

```bash
sudo systemd-nspawn -U # If the kernel supports the user namespaces feature, equivalent to:
sudo systemd-nspawn --private-users=pick --private-users-ownership=auto
# otherwise equivalent to:
sudo systemd-nspawn --private-users=no
```

```bash
sudo systemd-nspawn --boot # Automatically search for an init program and invoke it as PID 1, instead of a shell or a user supplied program. If this option is used, arguments specified on the command line are used as arguments for the init program.
```

## `--network-veth`

- the default if the `systemd-nspawn@.service` template unit file is used

```bash
sudo systemd-nspawn --network-veth # Create a virtual Ethernet link ("veth") between host and container. The host side of the Ethernet link will be available as a network interface named after the container's name (as specified with --machine=), prefixed with "ve-". The container side of the Ethernet link will be named "host0". The --network-veth option implies --private-network.
```
## `--settings`

Controls whether systemd-nspawn shall search for and use additional per-container settings from .nspawn files. Takes a boolean or the special values override or trusted.

- `/etc/systemd/nspawn/<machine>`
- `/run/systemd/nspawn/<machine>`

```bash
sudo systemd-nspawn --settings=true # (the default), a settings file named after the machine (as specified with the --machine= setting, or derived from the directory or image file name) with the suffix .nspawn is searched in /etc/systemd/nspawn/ and /run/systemd/nspawn/. If it is found there, its settings are read and used. If it is not found there, it is subsequently searched in the same directory as the image file or in the immediate parent of the root directory of the container. In this case, if the file is found, its settings will be also read and used, but potentially unsafe settings are ignored. Note that in both these cases, settings on the command line take precedence over the corresponding settings from loaded .nspawn files, if both are specified. Unsafe settings are considered all settings that elevate the container's privileges or grant access to additional resources such as files or directories of the host. For details about the format and contents of .nspawn files, consult systemd.nspawn

sudo systemd-nspawn --settings=override # the file is searched, read and used the same way, however, the order of precedence is reversed: settings read from the .nspawn file will take precedence over the corresponding command line options, if both are specified.

sudo systemd-nspawn --settings=trusted # the file is searched, read and used the same way, but regardless of being found in /etc/systemd/nspawn/, /run/systemd/nspawn/ or next to the image file or container root directory, all settings will take effect, however, command line arguments still take precedence over corresponding settings.

sudo systemd-nspawn --settings=false # no .nspawn file is read and no settings except the ones on the command line are in effect.
```
# INFO

- directly transitions into the service manager it doesn't go through an initrd
- doesn't bother with a boot loader, since containers after all share the same kernel

## keyboard: exit session

`Press ^] three times within 1s to exit session` --> CTRL + 5

## debootstrap

Debootstrap wird benutzt, um ein Debian-Basissystem völlig neu (from scratch) aufzusetzen, ohne dass dpkg oder apt installiert sind. Dies geschieht, indem .deb-Dateien von einem Spiegelserver abgerufen und sorgfältig in ein Verzeichnis installiert werden, in das schließlich mittels chroot(8) gewechselt wird.

## cdebootstrap

erzeugt Debian-Systeme von Grund auf neu.

Diese Implementierung unterscheidet sich von debootstrap durch eine andere Paketauswahl, die entsprechend der gewählten Variante erfolgt.

## systemd-nspawn vs machinectl

**systemd-nspawn** may be invoked directly from the interactive command line or run as system service in the background.

### service mode

In this mode each container instance runs as its own service instance; a default template unit file `systemd-nspawn@.service` is provided to make this easy, taking the container name as instance identifier. 

Note that containers started via _machinectl_ or `systemd-nspawn@.service` use different default options than containers started manually by the _systemd-nspawn_ command. 
The extra options used by the service are:

```bash
--boot # Managed containers automatically search for an init program and invoke it as PID 1
--network-veth # which implies --private-network – Managed containers get a virtual network interface and are disconnected from the host network
-U # Managed containers use the user_namespaces feature by default if supported by the kernel.
--link-journal=try-guest
```

This behavior can be overridden in per-container configuration
## systemd-nspawn vs docker

Nspawnd richtet sich an jene Admins, die wirklich nur grundlegende Kernel-Features nutzen wollen, um einzelne Prozesse voneinander zu isolieren.

Das Problem mit Podman und Docker ist nämlich, dass der Admin hier niemals nur das jeweilige Programm bekommt. Stattdessen haben Docker und Podman einen riesigen Haufen an Annahmen und Voraussetzungen dafür im Schlepptau, wie man einen Container gut und sinnvoll betreibt. Mit Fakten wie Volumes, Software Defined Networking und anderem Pipapo mag sich ein Administrator vielleicht gar nicht befassen, wenn er lediglich einen Apache-Prozess in ein virtuelles Gefängnis stecken will.

## systemd-nspawn@.service

```bash
systemctl cat systemd-nspawn@.service # view defaults
```

```sh
ExecStart=systemd-nspawn --quiet --keep-unit --boot --link-journal=try-guest --network-veth -U --settings=override --machine=%i
```
## systemd-networkd.service

Simply starting `systemd-networkd` on the host will provide the described container network auto-configuration **without interfering with the existing network setup**, as the existing interface(s) will remain _unmanaged_ by `systemd-networkd` until configured otherwise. As such, it is perfectly compatible with a managed network setup e.g. with NetworkManager, and starting `systemd-networkd` is really all that is needed to get Internet connectivity inside the containers (as well as to be able to reach them from the host) without disturbing anything on the host.
# HOW TO

## list available images/containers

```bash
machinectl list-images
sudo ll /var/lib/machines
```
## install a debian container

```bash
# Create a debian container:
sudo debootstrap --include=systemd,systemd-container,dbus stable /var/lib/machines/debian https://mirror.hs-esslingen.de/debian
```

## start container

### directly

- all options have to be passed on the command line

```bash
sudo systemd-nspawn --directory /var/lib/machines/debian -U --machine debian-vm
```

### as system service

- options have to be defined in unit files

```bash
systemctl cat systemd-nspawn@.service # view defaults
```

```bash
# Enable and start new guest Machine:
sudo machinectl enable debian # enable to start at boot
sudo machinectl start debian # start container as a system service

# Login:
sudo machinectl login debian-stable
```

## config vm

```bash
apt install sudo
systemctl enable --now systemd-networkd.service # if networking is desired
```

### use root user

```bash
# Set root pw in guest OS:
passwd
# Logout from guest OS:
exit
```

### add system user

```bash
useradd -s /usr/bin/bash app
passwd app # give a password and remember it
apt-get install -y chromium firefox-esr
logout
```

### add normal user


## config unit

- when started with `machinectl`
- https://www.freedesktop.org/software/systemd/man/latest/systemd.nspawn.html

### style 1: create a nspawn unit file

```bash
sudo nano /etc/systemd/nspawn/debian.nspawn
```

### style 2: override the service template unit file

view original config:

```bash
sudo machinectl start debian # start unit
```

```bash
sudo systemctl cat systemd-nspawn@debian.service # view defaults
```

```shell
[Service]
ExecStart=/usr/bin/systemd-nspawn --quiet --keep-unit --boot --link-journal=try-guest --network-veth -U --settings=override --machine=%i
```

Edit the configuration to match your network setup, and add any additional settings you require.

```shell
sudo systemctl edit systemd-nspawn@debian.service
```

```shell
[Service]
ExecStart=
ExecStart=/usr/bin/systemd-nspawn --quiet --keep-unit --boot \
                                  --link-journal=try-guest \
                                  --network-bridge=br0 -U \
                                  --settings=override \
                                  --machine=%i
```

Now it is possible to start the container with `machinectl start`.
## setup networking

- https://wiki.archlinux.org/title/Systemd-nspawn#Networking

_systemd-nspawn_ containers can use either **host networking** or **private networking**:

- **host networking**: the container has full access to the host network. This means that the container will be able to access all network services on the host and packets coming from the container will appear to the outside network as coming from the host (i.e. sharing the same IP address).
- **private networking**: the container is disconnected from the host's network. This makes all network interfaces unavailable to the container, with the exception of the loopback device and those explicitly assigned to the container.

The host networking mode is suitable for _**application containers**_ which do not run any networking software that would configure the interface assigned to the container.

The private networking mode is suitable for _**system containers**_ that should be isolated from the host system.

By default, the container will share the network of the host. This means that, subject to the firewall on the host, it can access the internet. Packets from the container are automatically masqueraded to appear that they come from the host. This needs no extra configuration, but gives the least separation between container and host.

### systemd-networkd.service

Enable and start `systemd-networkd.service` **on the host and in the container** to automatically provision the virtual link via DHCP and allow the container to access the host's network. 

```bash
systemctl status systemd-networkd.service
systemctl enable systemd-networkd.service
systemctl start systemd-networkd.service
```

### hostname

```bash
# Edit /etc/hostname
nano /etc/hostname
# (use a hostname name different from your host computer, something to recognize this guest machine appart from your host machine)

# Edit /etc/hosts
nano /etc/hosts
# Change first line to:
127.0.0.1       localhost <newhostname here>

# Update hostname on console:
hostname <newhostname>
```

### use host networking

You can disable private networking and make the container use host networking instead by adding following lines to `/etc/systemd/nspawn/_container-name_.nspawn`:

```sh
[Network]
VirtualEthernet=no
```

Replace 'container-name' with a name of your container.

## setup graphical vm

- you do not need to instal X11/Xorg display server, or wayland for containerOS

### wayland

- start with systemd-nspawn directly because of variable expansion

```sh
sudo systemd-nspawn \
  --directory /var/lib/machines/debian \
  --machine debian-vm \
  --network-veth \
  --bind=$XDG_RUNTIME_DIR/$WAYLAND_DISPLAY:/tmp/wayland.sock \
  --boot
```

```bash
# login and export the following environment variables:
export WAYLAND_DISPLAY=/tmp/wayland.sock
# use chromium:
export CHROMIUM_FLAGS="--ozone-platform-hint=wayland"
# use firefox:
MOZ_ENABLE_WAYLAND=1 firefox
```

### xorg

```bash
sudo systemd-nspawn \
--boot \
--directory /var/lib/machines/debian \
--volatile=no \
--bind-ro=${HOME}/.Xauthority \
--bind=/run/user/1000 \
--bind=/tmp/.X11-unix \
--bind=/dev/shm \
--bind=/dev/dri \
--bind=/run/dbus/system_bus_socket \
--bind=/YOUR_DATA_DIRECTORY
```

## troubles

```bash
# Allow outside login via local tty:
# may not be needed any more
echo 'pts/1' >> /etc/securetty
# 'pts/0' seems to be used when doing systemd-nspawn --boot, 'pts/1' with machinectl login
```