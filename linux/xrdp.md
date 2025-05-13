# xrdp

* https://www.digitalocean.com/community/tutorials/how-to-enable-remote-desktop-protocol-using-xrdp-on-ubuntu-22-04
* open-source implementation of RDP
* supports remote graphics, audio redirection, and clipboard transfer
* supports drive redirection that enables you to mount local drives on remote computers
* RDP transport is encrypted using *TLS* by default
* conserves bandwidth by compressing
* When using with a public Internet connection, an RDP gateway and SSH tunnel are recommended to improve security
* The `xrdp` service uses another service called `xrdp-sesman` to authenticate your user.

## setup

```sh
apt install xorgxrdp xrdp # 
```

```sh
grep xrdp /etc/passwd # user xrdp is created
```

**set default desktop cmd**:

```sh
ls /usr/share/xsessions/ # list available sessions
grep ^Exec= /usr/share/xsessions/cinnamon.desktop # grep the execution command for the Cinnamon desktop session
echo "cinnamon-session-cinnamon" > ~/.xsession # set your default session to Cinnamon Session
sudo systemctl restart xrdp # restart xrdp service
```

## config

```sh
sudo nano /etc/xrdp/xrdp.ini # open the xrdp configuration file
```

```sh
[Xorg]
name=Xorg
lib=libxup.so
username=ask
password=ask
ip=127.0.0.1
port=-1
code=20
```

## ssl

**create ssl keypair**

```sh
sudo openssl req -x509 -newkey rsa:2048 -nodes -keyout /etc/xrdp/key.pem -out /etc/xrdp/cert.pem -days 365
sudo chown xrdp:xrdp /etc/xrdp/key.pem 
sudo chown xrdp:xrdp /etc/xrdp/cert.pem
sudo adduser xrdp ssl-cert
```

**edit xrdp.ini**

```sh
# sudo nano /etc/xrdp/xrdp.ini

key_file=/etc/xrdp/key.pem # add your SSL key path
certificate=/etc/xrdp/cert.pem # add your SSL crt path

tls_ciphers=HIGH # set to high
ssl_protocols=TLSv1.2,TLSv1.3
crypt_level=high # set to High
security_layer=rdp,tls # You can use tls,rdp or tls
```

## log

```sh
journalctl -u xrdp.service -ef
journalctl -efu xrdp-sesman # The xrdp service uses another service called xrdp-sesman to authenticate your user
```
