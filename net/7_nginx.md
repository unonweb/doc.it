# INSTALL

```sh
sudo xed /etc/apt/sources.list
## Replace $release with your corresponding Ubuntu release
deb https://nginx.org/packages/ubuntu/ $release nginx
deb-src https://nginx.org/packages/ubuntu/ $release nginx

cat /etc/upstream-release/lsb-release # get the codename of ubuntu release

sudo apt update
sudo apt install nginx


## Replace $key with the corresponding $key from your GPG error.
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys $key
sudo apt update
sudo apt install nginx

# ABF5BD827BD9BF62 # key taken from error
```

# FILES

```sh
ll /etc/nginx/
sudo xed /etc/nginx/nginx.conf
```

# HOW TO

## create multiple virtual hosts

```sh
ll /etc/nginx/sites-available
sudo xed /etc/nginx/sites-available/manueldieterich
```

