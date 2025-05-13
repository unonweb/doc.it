* https://pypi.org/search

# PATHS

```sh
ll /usr/lib/python3/dist-packages
```

# pip

## install

```sh
pip3 install shell-gpt
pip3 uninstall shell-gpt
```

*However*... **you shouldn't really be installing Python programs with `pip`**. In short, sometimes those packages you install can interfere with the  ones that your system needs to function, so you might find yourself  unable to update or install any new packages with `apt`, or worse. Newer versions of Ubuntu will show you a warning about exactly that risk:

## config

```sh
# If none of --user, --global and --site are passed, a virtual environment configuration file is used if one is active and the file exists. Otherwise, all modifications happen to the user file by default.

pip3 config --global # Use the system-wide configuration file only
pip3 config --user # Use the user configuration file only
pip3 config --site # Use the current environment configuration file only

pip3 config --editor <editor> # Editor to use to edit the file. Uses VISUAL or EDITOR environment variables if not provided.

pip3 config edit --user # opens ~/.config/pip/pip.conf
pip3 config edit --global # opens /etc/pip.conf
pip3 config edit --site # opens /usr/pip.conf
```

## show

Show information about installed packages

```sh
pip3 show <pkg>
pip3 show pip # shows the default install path
pip3 show --files <pkg> # Show the full list of installed files for each package
```

## list

```sh
# list
pip3 list --outdated
pip3 list --user # Only output packages installed in user-site
```

# pipx

* erstellt eine neue virtuelle Umgebung für jede installierte Anwendung
* erstellt Links zu lokalen Binärdateien in **/bin** auf globaler Ebene

```sh
ll /home/frida/.local/pipx/venvs # irtual Environment location
ll /home/frida/.local/bin # Symlinks to apps
```



```sh
sudo apt update
sudo apt install pipx
pipx ensurepath
pipx install --include-deps ansible
```

```sh
pipx upgrade <pkg>
pipx upgrade-all # Upgrade all packages. Runs `pip install -U <pkgname>` for each package
```

```sh
pipx list # List installed packages
```



# packages

## shell-gpt

- requires OPENAI_API_KEY to be exported
```sh
python3 -m venv shellgpt # erstellt eine virtuelle Umgebung names "shellgpt"
source /bin/activate # aktiviert die virtuelle Umgebung

# export api key
env | grep -i open # check if environment variable is available

pip3 install shell-gpt --user
```

# venv

Create lightweight “virtual environments”, each with their own independent set of Python packages installed in their [`site`](https://docs.python.org/3/library/site.html#module-site) directories. A virtual environment is created on top of an existing Python installation, known as the virtual environment’s “base” Python, and may optionally be isolated from the packages in the base environment, so only those explicitly installed in the virtual environment are available.

```sh
python3 -m venv -h
python3 -m venv /path/to/new/virtual/environment
```
