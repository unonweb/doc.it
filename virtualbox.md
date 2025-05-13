# usb

1. install the extension pack (must be the same version as your VirtualBox)

# modules

```sh
modinfo vboxnetadp vboxnetflt vboxpci vboxdrv # show kernel modules
sudo rmmod vboxnetadp vboxnetflt vboxpci vboxdrv
sudo autoremove # to get rid of eventual dkms modules
sudo /sbin/rcvboxdrv setup
```

