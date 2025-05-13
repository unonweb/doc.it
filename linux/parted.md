## parted

gparted is the GUI tool

```sh
sudo apt install parted

sudo parted -l # lists partition layout on all block devices
sudo parted /dev/sda mklabel gpt
sudo parted -a optimal /dev/sda mkpart primary ext4 0% 100% 
# sets the default optimal alignment type
# makes a standalone (i.e. bootable, not extended from another) partition, using the ext4 filesystem
```
