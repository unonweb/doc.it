```sh
nano /etc/default/locale # get or edit default locale
locale -a # check which locales you currently have generated
```

# locale

get locale-specific information

```sh
locale # displays the current locale settings for each locale category based on the settings of the environment variables that control the locale
```

# locale-gen

* reads `/etc/locale.gen` and invokes `localedef` for the chosen localisation profiles.  
* run `locale-gen` after you have modified `/etc/locale.gen`

```sh
sudo nano /etc/locale.gen # You can generate more by editing and uncommenting the lines for the locales that you want to enable
locale-gen # generate the locles from
```

# install and change locale

```sh
sudo dpkg-reconfigure locales # check/un-check the languages you want to ‘install’ (generate)
```
