# fscrypt

* https://wiki.archlinux.org/title/Fscrypt

Alternatives to consider: To protect an entire file system with one password, block device encryption with `dm-crypt` (LUKS) is generally a better option, as it ensures that all files on the file system are encrypted, and also that all file system metadata is encrypted. `fscrypt` is most useful to encrypt specific directories, or to enable different encrypted directories to be unlockable independently—for example, per-user encrypted home directories. 

* If a user's home directory is encrypted, SSH for that user may not work until their home directory has been unlocked.

All officially supported kernels support native file encryption on ext4, F2FS, and UBIFS. 

```sh
# ext4
tune2fs -O encrypt /dev/device # enable later
mkfs.ext4 -O encrypt # enable when creating a new file system

# F2FS
mkfs.f2fs -O encrypt # when creating the file system 
fsck.f2fs -O encrypt # at a later time. 

# encrypt an empty directory
fscrypt encrypt dir
```
