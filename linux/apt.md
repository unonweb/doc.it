# REPOSITORIES

## ppa vs repository

* A repository is a collection of packages, hosted on an arbitrary server.
* A PPA is *also* a collection of packages, hosted on the **Launchpad** servers.

## distributions

### ubuntu

The default Ubuntu repositories are split into four components - main, restricted, universe and multiverse (third-party repositories generally have only one category)

* *Main*: Canonical-supported free and open-source software
* *Universe*: Community-maintained free and open-source software
* *Restricted*: Proprietary drivers for devices
* *Multiverse*: Software restricted by copyright or legal issues

### debian

* https://wiki.debian.org/SourcesList#Commonly_used_package_sources

* **DebianStable**: official Debian repository for the current release

* **StableProposedUpdates**: official Debian repository for upcoming point releases (security and important bug fixes every ~2 months)
* **StableUpdates**: official Debian repository for changes that cannot wait for the next point release, packages are also added to StableProposedUpdates for inclusion in the next point release
* **DebianSecurity**: official Debian repository for frequent security updates
* **DebianBackports**: more recent versions of some packages, compatible with DebianStable.
* **DebianTesting**: current development state of the next stable Debian distribution
* **DebianUnstable**: rolling development version containing the latest packages
* **DebianExperimental**: development version containing the experimental/alpha/beta/untested packages 

#### component

* **main** consists of DFSG-compliant packages, which do not rely on software outside this area to operate. These are the only packages considered part of the Debian distribution.
* **contrib** packages contain DFSG-compliant software, but have dependencies not in main (possibly packaged for Debian in non-free).
* **non-free** contains software that does not comply with the DFSG. 

### examples

```sh
deb http://deb.debian.org/debian bookworm main non-free-firmware
deb-src http://deb.debian.org/debian bookworm main non-free-firmware

deb http://security.debian.org/debian-security bookworm-security main non-free-firmware
deb-src http://security.debian.org/debian-security bookworm-security main  non-free-firmware

deb http://deb.debian.org/debian bookworm-updates main non-free-firmware
deb-src http://deb.debian.org/debian bookworm-updates main non-free-firmware
```

## .list

* /etc/apt/sources.list
* /etc/apt/sources.list.d

### syntax

* If options are provided, they are space-separated and all together are enclosed within square brackets ( `[]` )
* Options allowing multiple values should separate each value with a comma ( `,` ) and each option name is separated from its values by `=`

```sh
deb http://site.example.com/debian distribution component1 component2 component3
deb-src http://site.example.com/debian distribution component1 component2 component3

deb http://de.archive.ubuntu.com/ubuntu jammy main restricted
```

Der Eintrag ist dabei in vier Sektionen gegliedert:

- **type**: `deb` oder `deb-src` 

- **url**: z.B. `http://de.archive.ubuntu.com/ubuntu` (oder auch eine CD/DVD) 

- **distribution**: `jammy`, `jammy-updates`, `jammy-security`

  * either the release code name: stretch, buster, bullseye, bookworm
  * or the release class: `oldstable`, `stable`, `testing`, `unstable`

- **components**: (optionale Liste): `main`, `restricted`, `universe`, `multiverse`

  * `main` consists of DFSG-compliant packages, which do not rely on software outside this area to operate. These are the only packages considered part of the Debian distribution.

  * `contrib` packages contain DFSG-compliant software, but have dependencies not in main (possibly packaged for Debian in non-free).
  * `non-free` contains software that does not comply with the DFSG. 

## Deb822 (.sources)

* https://blobfolio.com/2022/deb822-and-apt-key/

Ubuntu verwendet ab Noble Numbat [24.04](https://wiki.ubuntuusers.de/24.04/) standardmäßig nicht mehr die Datei **/etc/apt/sources.list** und allgemein auch nicht mehr die in diesem Artikel beschriebenen  Paketquellen im alten, einzeiligen Format in Dateien mit Namensendung **.list**, sondern nun Dateien im Format deb822 mit der Endung **.sources**.

### format specifications

```sh
Types: "deb" # "deb-src"
# Defines which types of packages to look for from a given source; either binary: deb or source code: deb-src. The deb type references a typical two-level Debian archive providing packages containing pre-compiled binary data intended for execution on user machines. The deb-src type references a Debian distribution’s source code in the same form as the deb type. A deb-src line is required to fetch source pacakge indices.

URIs: http://security.ubuntu.com/ubuntu/
# must specify the base of the Debian distribution archive, from which APT finds the information it needs. There must be a URI component present in order for the source to be valid; multipls URIs can be configured simultaneously by adding a space-separated list of URIs.

Suites: noble noble-updates noble-backports
# Can specify an exact path in relation to the URIs provided, in which case `Components` must be omitted and suite must end with a slash.
# Alternatively, it may take the form of a distribution version (version codename). If the suite does not specify a path, at least one deb822-field-component must be present.

Components: main restricted universe multiverse # required: see Suites
# Specify different sections of one distribution version present in a Suite. 
# If `Suites` specifies an exact path, then no Components may be specified. Otherwise, a component must be present.
```

#### optional

```sh
Enabled: yes # Disabled sources are not queried for package lists, effectively removing them from the system sources while still allowing reference or re-enabling at any time.

X-Repolib-Name: <filename>
# Defines a formatted name for the source which is suitable for inclusion within a graphical UI or other interface which presents source information to an end-user. As a repolib-module specific field, this is silently ignored by APT and other tools operating with deb822 sources and is only intended to be utilized by repolib-module itself.
```

### example

```sh
Types: deb
URIs: http://de.archive.ubuntu.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb
URIs: http://security.ubuntu.com/ubuntu/
Suites: noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

X-Repolib-Name: Google Chrome
Enabled: yes
URIs: http://dl.google.com/linux/chrome/deb
Suites: stable
Components: main
Architectures: amd64
```

## add repository key

### location

* `/usr/share/keyrings`: they should all follow the basic naming pattern of `{THE_REPO}-archive-keyring.gpg`
  * official repo keys from ubuntu, mate, debian
* `/etc/apt/keyrings/`
  * node.asc
  * signal.asc


**Schlüssel von Website**

```sh
wget -O- https://[Webadresse]/apt/keys.asc | sudo -H gpg --dearmor -o sudo tee /etc/apt/keyrings/xyz.gpg
```

**Schlüssel vom Keyserver**

```sh
sudo gpg --no-default-keyring --keyring /etc/apt/keyrings/[Repository]-archive-keyring.gpg --keyserver --recv-keys [Fingerprint]
```

### how To

```sh
# typora
# 1. Download the key and save it locally somewhere
curl https://typora.io/linux/public-key.asc | gpg --dearmor > /usr/share/keyrings/typora.gpg
# 2. Create repo file typora.list in /etc/apt/sources.list.d.
sudo vim /etc/apt/sources.list.d/typora.list
# 3. Paste repo definition into this file:
deb [arch=amd64 signed-by=/usr/share/keyrings/typora.gpg] https://typora.io/linux ./
# 4.Now you can install Typora:
sudo apt-get update
sudo apt-get install typora
```

The **key needs to be owned by root, but readable by everyone**. If  either of those don't happen, you'll need to fix it manually with:

```bash
# Fix the permissions:
sudo chmod 644 /usr/share/keyrings/sublimetext-archive-keyring.gpg

# Fix the ownership:
sudo chown root: /usr/share/keyrings/sublimetext-archive-keyring.gpg
```

### embedded keys

Inline keys use the same `Signed-By:` field as standalone keys. The only difference is that instead of a path, you feed it the entire (armored) key, like:

```
Signed-By:
 -----BEGIN PGP PUBLIC KEY BLOCK-----
 .
 thegibberishasciikeydatagoesherethegibberishasciikeydatagoeshere
 thegibberishasciikeydatagoesherethegibberishasciikeydatagoeshere
 thegibberishasciikeydatagoesherethegibberishasciikeydatagoeshere
 thegibberishasciikeydatagoeshere
 -----END PGP PUBLIC KEY BLOCK-----
```

There are three things to note:

1. Each line of the key must be indented with a single space.
2. Empty lines must be represented with a dot.
3. The BEGIN/END markers (and blank line before the hash) must always be written *exactly* as shown above. (Ignore your key's actual markers if different, and use these instead.)

That's all there is to it!

## add-apt-repository

Adds a repository into the /etc/apt/sources.list or /etc/apt/sources.list.d or removes an existing one

### list

```sh
sudo add-apt-repository --list # List currently configured repositories.
# This will only list enabled repositories; it will not show disabled repository lines.
# By default, this shows only binary (deb) repositories.  

sudo add-apt-repository --list --enable-source # also show source (deb-src) repositories
```

### remove

```sh
sudo add-apt-repository --remove # Remove the specified repository.
# This first will disable (comment out) the matching line(s), and then any modified file(s) under sources.list.d/ will be removed if they contain only empty and commented lines.
```

### ppa

```sh
sudo add-apt-repository ppa:<ppa_name>
sudo add-apt-repository ppa:LAUNCHPAD-NUTZERNAME/PPA-NAME 
```

```sh
sudo add-apt-repository --ppa # Add an Ubuntu Launchpad Personal Package Archive. 
# Must be in the format ppa:USER/PPA, USER/PPA, or USER.
# The USER parameter should be the Launchpad team or person that owns the PPA.  The PPA parameter should be the name of the PPA; if not provided, it defaults to 'ppa'.
# The GPG public key of the PPA will also be downloaded and added to apt's keyring
```

### uri

```sh
add-apt-repository --uri # Add an archive, specified as a single URI. 
# If the URI provided is detected to be a PPA, this will operate as if the --ppa parameter was used.
```

# CONFIG

```sh
sudo nano /etc/apt/apt.conf
```

## DPkg

Pre-Invoke, Post-Invoke

### readonly /usr

```js
DPkg  
{  
    // Auto re-mounting of readonly /usr      
    Pre-Invoke {"mount -o remount,rw /usr";};  
    Post-Invoke {"mount -o remount,ro /usr";};  
}
```

```js
DPkg {
    // Auto re-mounting of a readonly /
    Pre-Invoke { "mount -o remount,rw /"; };
    Post-Invoke { "test ${NO_APT_REMOUNT:-no} = yes || mount -o remount,ro /usr || true"; };
};
```

The environment variable NO_APT_REMOUNT can be set to yes to prevent apt remounting the filesystem read-only. This is handy, if you plan to configure the installed package or make other changes in /etc.

if unattended-upgrades is used you (also) have to modify `apt-daily-upgrade.service`

```sh
# sudo systemctl edit apt-daily-upgrade.service
[Service]
ExecStartPre=-mount -o remount,rw /
ExecStartPost=-sh -c 'if [ "${NO_APT_REMOUNT:-no}" != yes ]; then mount -o remount,ro /; fi'
```

#### troubleshooting

> which always works for mounting rw but does not always work for remounting ro because after install/upgrades for some reason mount thinks /usr is busy and refuses to remount it read only. very irritating especially since i have not found any files opened with write permission with fuser...

> If you replace a program which is running, the old executable file is not actually deleted until the last instance of the program has terminated. Therefore the file system cannot be emounted read only.  If you kill or restart all such programs, you should be able to remount /usr read only.


# CLI

- **advanced packaging tool**
- reicht die Pakete zur Installation an `dpkg` weiter
- Abhängigkeiten werden zwar bei der Installation, nicht aber bei der Deinstallation automatisch berücksichtig
- apt fasst die wichtigsten Kommandos von `apt-get` und `apt-cache` zusammen, ist benutzerfreundlicher und interaktiver

```sh
sudo apt edit-sources # Bearbeitung der sources.list; stellt außerdem grundlegende Plausibilitätsprüfungen bereit.
sudo apt download <pkg> # läd das Paket aus dem Repo in das aktuelle Verzeichnis (keine Zielangabe!)
```

## show | search

```sh
apt show <pkg> # Informationen über Paket (Abhängigkeiten, Größe, Quellen, Beschreibung, etc.)
```

```sh
apt search <pattern> # Liste verfügbarer Pakete nach pattern durchsuchen (anschaulicher als apt-cache search); zeigt ggf. an, wenn ein Paket schon installiert ist
```

## install

* **recommends** werden per default installiert, **suggests** nicht
* falls ein Paket (in einer veralteten Version) schon existiert, wird ein Upgrade durchgeführt

```sh
apt install <pkg1> <pkg2> # 
apt install --fix-broken # without package! Fehler beheben; versucht ein System von vorhandenen, beschädigten Abhängigkeiten zu befreien; -f
```

## list

```sh
apt list
apt list <pkg> # alleine zeigt ggf. auch an, ob ein Paket installiert ist
apt list --installed # listet installierte Pakete; Alternative: dpkg -l or --list
apt list --installed | grep <pkg> # sucht unter den installierten Paketen nach Paket
apt list --upgradable # listet Pakete für die Aktualisierungen vorliegen (vorher: apt update)
apt list --installed | grep -F \[installed\] # lists packages installed by user command
apt list --manual-installed=true
```

```sh
apt list linux-image* | grep installed # list installed kernel version
```

## update

```sh
apt update # Paketinformationen herunterladen
```

## upgrade

```sh
apt upgrade # install available upgrades of all packages currently installed on the system from the sources configured via sources.list. New packages will be installed if required to satisfy dependencies, but existing packages will never be removed. If an upgrade for a package requires the removal of an installed package the upgrade for this package isn't performed
```

## full-upgrade

Hin und wieder muss man mit diesem Befehl jedoch auch vorsichtig sein – gerade unter Debian Testing und noch eher unter Debian Sid (Unstable), werden Pakete nicht so veröffentlicht, dass man das komplette System auf einem Schlag aktualisieren kann. Ganz einfach – weil nötige Abhängigkeiten noch nicht hochgeladen wurden. Wird etwa das Paket „firefox-data“ als Aktualisierung angeboten, aber es ist noch das Paket „firefox“ in der alten Version vorhanden, könnte der Befehl vorschlagen, Firefox einfach komplett zu deinstallieren – der Browser wäre weg. Würden also Pakete deinstalliert, die Sie absolut benötigen – sollte der Befehl nicht bestätigt werden, in diesem Fall müssen Sie einfach abwarten, bis der Entwickler die benötigte Abhängigkeit zur Verfügung stellt.

```sh
sudo apt full-upgrade # performs the function of upgrade but will remove currently installed packages if this is needed to upgrade the system as a whole
```

# APT-KEY

`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF`

# SYSTEMD UNITS

The following units are installed by apt to run **periodic APT maintenance tasks**

-  `apt-daily.service`
   Periodically updates the package cache to ensure the system has the latest information about available package updates.
- `apt-daily-upgrade.service`
   Checks for and installs any available package updates automatically, without user intervention.

```sh
/lib/systemd/system/apt-daily.service # calls apt.systemd.daily update
/lib/systemd/system/apt-daily-upgrade.service # calls apt.systemd.daily install
/lib/systemd/system/apt-daily-upgrade.timer
/lib/systemd/system/apt-daily.timer
```

```sh
# override
sudo systemctl edit apt-daily.timer
sudo systemctl edit apt-daily.service
# creates: /etc/systemd/system/apt-daily.timer.d/override.conf

sudo systemctl edit apt-daily-upgrade.timer
sudo systemctl edit apt-daily-upgrade.service
# creates: /etc/systemd/system/apt-daily-upgrade.timer.d/override.conf
```

```sh
# check unit activity
sudo journalctl -u apt-daily.service
sudo journalctl -u apt-daily-upgrade.service
```

```sh
# show script
nano /usr/lib/apt/apt.systemd.daily
```

## built-in mechanism vs unattended-upgrades

`apt-daily` services provide only a basic level of automatic updates

Advantages of the `unattended-upgrades` package:

- **Granularity of Control**
  You can configure it to only upgrade security updates, or to include or exclude specific packages.
- **Granularity of Control**
    - Support for automatically removing unused packages
    - Sending email notifications for upgrade results
- **Handling of Reboots**