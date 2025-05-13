# fuse

- https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html
- https://manpages.debian.org/unstable/fuse/mount.fuse.8.en.html
- https://wiki.ubuntuusers.de/FUSE/

**Filesystem in Userspace** (FUSE) is a simple interface for userspace programs to export a virtual filesystem to the Linux kernel. It also aims to provide a secure method for non privileged users to create and mount their own filesystem implementations.

- **FUSE**:
  The in-kernel filesystem that forwards requests to a user-space process.
- **libfuse**:
  The shared library that most (user-space) filesystems use to communicate with FUSE (the kernel filesystem). libfuse also provides the **fusermount3** helper to allow non-privileged users to mount filesystems.

FUSE wird verwendet von:
- **sshfs**
  Entfernte Rechner in das System einbinden
- **NTFS-3G**
  Erweiterter Schreib-/Lesezugriff auf NTFS-Partitionen. Ist in Ubuntu Standard.
- **exfat-fuse**
  vor Ubuntu 22.04 für exFAT erforderlich.
- **gio mount**
  Auch das GIO/GVfs verwendet FUSE.

```sh
man fuse

allow_other # This option overrides the security measure restricting file access to the filesystem owner, so that all users (including root) can access the files.
```

```sh
sudo nano /etc/fuse.conf
```

## fusermount3

a program to mount and unmount FUSE filesystems. It should be called directly only for unmounting FUSE file systems. To allow mounting and unmounting  by  unprivileged  users,  fusermount3 needs to be installed **set-uid root**.


## troubles

option **allow_other** only allowed if 'user_allow_other' is set in /etc/fuse.conf