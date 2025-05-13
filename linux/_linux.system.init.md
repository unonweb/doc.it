# config

```sh
sudo rm /etc/apt/preferences.d/nosnap.pref
```

# typora

```sh
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
wget -qO - https://typora.io/linux/public-key.asc | sudo tee /etc/apt/trusted.gpg.d/typora.asc
# add Typora's repository
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update
# install typora
sudo apt-get install typora
```

# signal

```sh
# 1. Install our official public software signing key:
wget -O- https://updates.signal.org/desktop/apt/keys.asc | gpg --dearmor > signal-desktop-keyring.gpg
cat signal-desktop-keyring.gpg | sudo tee /usr/share/keyrings/signal-desktop-keyring.gpg > /dev/null

# 2. Add our repository to your list of repositories:
echo 'deb [arch=amd64 signed-by=/usr/share/keyrings/signal-desktop-keyring.gpg] https://updates.signal.org/desktop/apt xenial main' | sudo tee /etc/apt/sources.list.d/signal-xenial.list

# 3. Update your package database and install Signal:
sudo apt update && sudo apt install signal-desktop
```

# other

```sh
curl https://vscode.download.prss.microsoft.com/dbazure/download/stable/611f9bfce64f25108829dd295f54a6894e87339d/code_1.90.1-1718141439_amd64.deb > code_1.90.1-1718141439_amd64.deb
sudo apt install ./<file>.deb
```



```sh
sudo apt install python3-pip
sudo apt install ssh
sudo apt install nfs-kernel-server
sudo apt install git
sudo apt install snapd

#export
dconf dump /org/mate/ > mate.txt
#import
dconf load /org/mate/ < mate.txt

flatpak install com.visualstudio.code
```

# ansible

```sh
sudo add-apt-repository --yes --update ppa:ansible/ansible
```

