# install

## debian

To install the microcode update packages from the latest point-release, enable *contrib* and *non-free* in */etc/apt/sources.list* if they're not already enabled: 

```sh
deb http://deb.debian.org/debian bullseye main contrib non-free
deb http://security.debian.org/debian-security bullseye-security main contrib non-free
deb http://deb.debian.org/debian bullseye-updates main contrib non-free
```

You will have to enable both `contrib` and `non-free` in `/etc/apt/sources.list`. For Debian 12 please remember to also enable non-free-firmware.

```sh
sudo apt install amd64-microcode # for systems with AMD AMD64 processors
sudo apt install intel-microcode # for systems with Intel processors
```

# check

**check the microcode version of your CPU**:

```sh
grep -E 'family|model|stepping|microcode' /proc/cpuinfo | head -5
```

**check if the kernel updated the microcode on boot**

```sh
journalctl -k | grep "microcode:"
# microcode: microcode updated early to revision 0x28, date = 2019-11-12
# That means the microcode image in the BIOS is older, thus, the Kernel loaded a newer version
```

**check if microcode package is installed/upgradable**

```sh
apt list intel-microcode
apt upgrade intel-microcode
```



**check if there's a microcode update**

```sh
sudo apt-get install cpuid
```

# update

## via OS (early loading)

Steht für ein bestimmtes Mainboard noch kein BIOS Update mit aktualisiertem Microcode bereit oder gibt es aufgrund organisatorischer  Einschränkungen (24x7 Betrieb) keine Möglichkeit ein entsprechendes Update einzuspielen, können Microcode Updates auch häufig über das  Betriebssystem eingespielt werden. In diesem Fall kopiert das  Betriebssystem möglichst bald während des Startvorganges des  Betriebssystems den neuen Microcode in die CPU. Dieses *early loading* geschieht jedoch naturgemäß später als es bei einem Microcode Update  über das BIOS der Fall ist. Intel empfiehlt daher nach Möglichkeit ein Microcode Update über ein BIOS Update durchzuführen. Der Hersteller  weißt auch darauf hin, dass nicht alle Microcode Updates über das  Betriebssystem eingespielt werden können.

## via firmware update

