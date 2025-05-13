# LINKS

- https://wiki.ubuntuusers.de/efibootmgr/
# EFIBOOTMGR

manipulate the EFI Boot Manager

* create and destroy boot entries
* change the boot order
* change the next running boot option

```sh
sudo efibootmgr # displaying the current settings
sudo efibootmgr --timeout <seconds> # Boot Manager timeout, in seconds
sudo efibootmgr --verbose #
```

```sh
BootCurrent: 0000
Timeout: 1 seconds
BootOrder: 0000,000B,0013,0014,0015,0009
Boot0000* ubuntu
Boot0009* ASUS     DRW-24F1ST   a
Boot000B* ST2000DM001-1CH164
Boot0013* SanDisk Ultra II 250GB
Boot0014* ST1000LM024 HN-M101M 2AR1
Boot0015* UEFI: ST1000LM024 HN-M101M 2AR1
```

```sh
sudo efibootmgr --delete-bootnum
```

# HOW TO
## change boot order

```sh
efibootmgr # show current boot order
sudo efibootmgr --bootorder XXXX,YYYY,ZZZZ # change boot order
```
