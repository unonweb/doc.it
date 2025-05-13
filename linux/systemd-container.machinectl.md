
- controlls containers located in `/var/lib/machines/` 
- **controls instances of the `systemd-nspawn@.service` unit**

- `/var/lib/machines`: container images

```bash
journalctl -M <container>
```
# CLI

```sh
machinectl login # open an interactive terminal login session on the local host
machinectl login <machine> # open an interactive terminal login session on <machine>
# creates a TTY connection to a machine or the local host and asks for the execution of a getty on it
machinectl list # List currently running (online) virtual machines and containers.
machinectl list --all # 
```

```bash
machinectl start <name> # Start a container as a system service, using systemd-nspawn(1). This starts systemd-nspawn@.service, instantiated for the specified machine name, similar to the effect of systemctl start on the service name.  systemd-nspawn looks for a container image by the specified name in /var/lib/machines/ (and other search paths, see below) and runs it. Use list-images (see below) for listing available container images to start.

# To interactively start a container on the command line with full access to the container's console, please invoke systemd-nspawn directly.
```

```bash
# Enable or disable a container as a system service to start at system boot, using systemd-nspawn
machinectl enable <name>
machinectl disable <name>
```

**mount a host directory into the container:**

```bash
machinectl --bind=/path/on/host
machinectl --bind-ro=/path/on/host # the specified path will be mounted from the host to the same path in the container

machinectl --bind=/path/on/host:/path/in/container # map path
```


```bash
machinectl poweroff # stop a running container
```