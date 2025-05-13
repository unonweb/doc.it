# Nextcloud

# install

* https://www.its-farin.de/nextcloud-installation-leicht-gemacht-ein-leitfaden-fuer-die-eigene-cloud/

### nextcloud

```sh
cd /tmp
wget https://download.nextcloud.com/server/releases/latest.zip 
sudo unzip latest.zip 
sudo mv nextcloud /var/www/
```

### php

```sh
apt install php8.1 php8.1-curl php-pear php8.1-bcmath php8.1-xml php-dompdf php8.1-mbstring libmagickcore-6.q16-6-extra php8.1-zip php8.1-imagick  php8.1-zip php8.1-intl php8.1-gd php8.1-redis php8.1-cli php8.1-mbstring php8.1-xml php8.1-mysql php8.1-curl php8.1-apcu php8.1-gmp php8.1-gd  libapache2-mod-php8.1
```

```sh
# upgrade php and modules
sudo apt install php8.2 php8.2-curl php8.2-bcmath php8.2-xml php8.2-mbstring php8.2-zip php8.2-imagick  php8.2-zip php8.2-intl php8.2-gd php8.2-redis php8.2-cli php8.2-mbstring php8.2-xml php8.2-mysql php8.2-curl php8.2-apcu php8.2-gmp php8.2-gd  libapache2-mod-php8.2
```

```sh
sudo a2enconf libapache2-mod-php8.2 # enable new module
sudo a2disconf libapache2-mod-php8.1 # disable old module
```

```sh
sudo a2dismod php8.1
sudo a2enmod php8.2
```

# config

* https://docs.nextcloud.com/server/30/admin_manual/installation/harden_server.html

## php

```sh
sudo nano /etc/php/8.1/apache2/php.ini
sudo nano /etc/php/8.2/apache2/php.ini
```

```sh
opcache.interned_strings_buffer = 24
memory_limit = 1024M
upload_max_filesize = 16G
post_max_size = 16G
date.timezone = Europe/Berlin
output_buffering = Off
```

### apache

```sh
sudo nano /etc/apache2/sites-available/nextcloud.conf
```

```html
<VirtualHost *:80>
     ServerAdmin postmaster@DOMAIN.de
     DocumentRoot /var/www/nextcloud/
     ServerName SUBDOMAIN.DOMAIN.DE

     <Directory /var/www/nextcloud/>
        Options +FollowSymlinks
        AllowOverride All
        Require all granted
          <IfModule mod_dav.c>
            Dav off
          </IfModule>
        SetEnv HOME /var/www/nextcloud
        SetEnv HTTP_HOME /var/www/nextcloud
     </Directory>
     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

# how to

## embed nextcloud as iframe

* https://return2.net/nextcloud-enable-external-iframe/

```sh
sudo nano /var/www/nextcloud/lib/public/AppFramework/Http/ContentSecurityPolicy.php

sudo nano /var/www/nextcloud/lib/private/legacy/OC_Response.php
// header('X-Frame-Options: SAMEORIGIN'); // Disallow iFraming from other domains
```

# occ

* https://docs.nextcloud.com/server/26/admin_manual/configuration_server/occ_command.html

`occ` is a PHP script and you need to run it as the web server user

```sh
sudo -u apache php /var/www/nextcloud/occ maintenance:repair --include-expensive
```