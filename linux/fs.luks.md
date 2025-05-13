# how to

## automatically decrypt additional partition

### create keyfile

```sh
sudo dd if=/dev/urandom of=/root/lukskeyfile bs=1024 count=4
sudo chmod 0400 /root/lukskeyfile
```

### cryptsetup

```sh
sudo cryptsetup luksAddKey  <device> /root/lukskeyfile
sudo cryptsetup luksAddKey  /dev/nvme0n1p1 /root/lukskeyfile
# The <device> parameter can also be specified by a LUKS UUID in the format UUID=<uuid>. Translation to real device name uses symlinks in /dev/disk/by-uuid directory.
```

### edit crypttab

- first field: **target**
  describes the mapped device name. It must be a plain filename without any directory components. A mapped device which encrypts/decrypts data to/from the source device will be created at /dev/mapper/target by cryptsetup.
  
- second field: **source device**
  describes either the block special device or file that contains the encrypted data.
  
- third field: **key file**
  describes the file to use as a key for decrypting the data of the source device. In case of a keyscript, the value of this field is given as argument to the keyscript. Note that the entire key file will be used as the passphrase; *the passphrase must not be followed by a newline character*.

- fourth field: **options**
  an optional comma-separated list of options and/or flags describing the device type (luks, tcrypt, bitlk, fvault2, or plain which is also the default) and cryptsetup options associated with the encryption process.

```sh
blkid -t TYPE=crypto_LUKS

sudo nano /etc/crypttab
```

```txt
luks-bacd528d-c003-43e8-b806-0d0afb1352e9 UUID=bacd528d-c003-43e8-b806-0d0afb1352e9 /root/lukskeyfile luks
```
### update initramfs

```sh
sudo update-initramfs -u
```
