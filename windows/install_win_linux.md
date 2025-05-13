# woeusb

WoeUSB is a program that can be run without installation:

1. Download the program(woeusb-N.N.N.bash) from [the Releases page](https://github.com/WoeUSB/WoeUSB/releases)
2. Fix the missing executable file permission (`chmod +x path/to/woeusb-N.N.N.bash`)
3. Launch a terminal application and run the program via the appropriate path(`sudo path/to/woeusb-N.N.N.bash --help`)

##### dependencies

```sh
sudo apt install \
    bash \
    dosfstools \
    findutils \
    grep \
    gawk \
    grub-common \
    grub-pc-bin \
    ntfs-3g \
    p7zip-full \
    parted \
    util-linux \
    wget \
    wimtools
```



```sh
sudo ./woeusb-5.2.4.bash --device ~/software/win/Win8.1_English_x64.iso /dev/sdc
```

