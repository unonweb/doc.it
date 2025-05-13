# links

`chmod` never changes the permissions of symbolic links; the chmod system call cannot change their permissions. This is not a problem since the **permissions of symbolic links are never used**. However, for each symbolic link listed on the command line, chmod changes the permissions of the pointed-to file.

`chown`, by default it changes the *target* of the symbolic link

If you'd like to change ownership of the link itself, you need to use the `-h` option to `chown`:

```sh
chown --no-dereference #  affect each symbolic link instead of any referenced file (useful only on systems that can change the ownership of a symlink); -h
```
