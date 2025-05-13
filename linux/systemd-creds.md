* https://www.freedesktop.org/software/systemd/man/latest/systemd-creds.html
* https://smallstep.com/blog/systemd-creds-hardware-protected-secrets/
* https://torsion.org/borgmatic/docs/how-to/provide-your-passwords/

```bash
systemd-creds has-tpm2
sudo systemd-creds setup # set up the host credentials
```

```bash
systemd-ask-password -n | systemd-creds encrypt - /etc/credstore.encrypted/borgmatic.pw # save pw as an encrypted credential
```