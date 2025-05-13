# LINKS

- https://gtfobins.github.io/
# FS
## symlink attack

- https://www.halfdog.net/Security/2010/FilesystemRecursionAndSymlinks/

- privileged script writes to file /tmp/example.txt
- attack: file is symlinked to /root/.ssh/authorized_keys
- script overwrites the content of authorized_keys next time it is executed

# SUDO

## alias attack

- https://iniguez.dev/blog/2020_11_18-stealing_passwords_with_alias/

```sh
# .bashrc
alias sudo='sudo <malicious-program>; sudo'
```