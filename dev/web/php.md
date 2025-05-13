# list

```sh
sudo apt list *php8.1* | grep installed # lists all installed php8.1 modules
```

# upgrade

* https://php.watch/articles/install-php82-ubuntu-debian

```bash
sudo dpkg -l | grep php | tee packages.txt
sudo add-apt-repository ppa:ondrej/php # Press enter when prompted.
sudo apt update
sudo apt install php8.2 php8.2-cli php8.2-{bz2,curl,mbstring,intl}

# apache
sudo apt install libapache2-mod-php8.2
sudo a2enconf libapache2-mod-php8.2
sudo a2disconf libapache2-mod-php8.1 # disable old module

## Remove old packages
sudo apt purge php8.1*
```

# multiple versions

The PHP 8.2 CLI will be installed at `/usr/bin/php8.2` location by default, but are linked Similarly, other PHP binary files will be located in the same directory (`/usr/bin/php8.0`, `/usr/bin/php7.4`, etc). The default `php` name will be symlinked to the latest PHP version by default, but it is possible to change where the default `php` command links to.

The `update-alternatives` command provides an easy way to switch between PHP versions for PHP CLI if there are multiple PHP versions linking to the same `php` path.

```bash
sudo update-alternatives --config php
```