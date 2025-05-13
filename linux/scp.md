scp
----------

secure copy copies files between hosts on a network; uses ssh; will ask for passwords or passphrases if they are needed for authentication.

```sh
scp <file> <user>@<host>:<path>
scp <options> <source> <target>

scp -i <private-key-file> # from which the identity (private key) for public key authentication is read; this is directly passed to ssh
scp -o <ssh-option> # can be used to pass options to ssh
scp -P <port> # specifies the port to connect to on the remote host
scp -v # verbose mode
scp -r # recursively
```

```sh
# from the local host to a remote host (file.txt):
scp file.txt user@host.com:/path/to/directory

# from the local host to a remote host (directory):
scp -r directory user@host:/some/remote/directory/

# from a remote host to the local host (file.txt):
scp usere@host:file.txt /some/local/directory

# from one remote host to another remote host (file.txt):
scp user@host:/some/remote/directory/file.txt username@remote2:/some/remote/directory/
```

```sh
scp -P 151 ./test payload@localhost # executed as frida --> owner will be frida
scp -P 151 /etc/systemd/user/un-payload-prod.service payload@strato:/etc/systemd/user/un-payload-prod.service
```

```sh
scp user@host:/path/to/file.tar . # copy to your local directory
```

## troubleshooting

**copy contents or directories**:

```sh
scp -r frida@lem:/srv/web/ /srv/web/ # wrong! --> /srv/web/web
scp -r frida@lem:/srv/web /srv # right --> /srv/web
```

**ssh connects but no copy operation**: One possible cause of trouble is having **any** message print out during the login process on server.

```sh
# print msg only in interactive terminals:
if [[ -n "$PS1" ]]; then
  echo "parsing '.profile'"
fi
```
