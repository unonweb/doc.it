# woff2

```sh
# install from repo
sudo apt install woff2
```

```sh
# install from github
git clone --recursive https://github.com/google/woff2.git
cd woff2
make clean all

# add to path
xed ~/.profile
```

```sh
# use
woff2_compress myfont.ttf
woff2_decompress myfont.woff2
```

```sh
# convert all .ttf files found starting from .
find "/home/frida/.local/share/fonts/Google Fonts" -name "*.ttf" -exec woff2_compress {} \; # convert
find "/home/frida/.local/share/fonts/Google Fonts" -name "*.woff2" -execdir mv {} /srv/web/resources/fonts/{} \; # move
find "/home/frida/.local/share/fonts/Google Fonts" -name "*.woff2" -execdir rsync --archive --human-readable --delete --progress {} /srv/web/resources/fonts/ \; # sync

rsync --archive --human-readable --delete --progress "/home/frida/.local/share/fonts/Google Fonts/Fresca/Fresca_Regular.22.ttf" /srv/web/resources/fonts/
```

```sh
find . -name "*.ttf" -exec woff2_compress {} \; # convert
```

