# FLATPAK

- https://github.com/flathub/org.mozilla.Thunderbird

```sh
ll ~/.var/app/org.mozilla.Thunderbird/.thunderbird
```
#### Migration from pre-exisiting non-flatpak installations

```sh
cp -r ~/.thunderbird ~/.var/app/org.mozilla.Thunderbird/
```

In case Thunderbird opens a new profile instead of the existing one, run:  

```sh
flatpak run org.mozilla.Thunderbird -P
```

then select the right profile and tick "_Use the selected profile without asking on startup_" box.

#### Wayland

To enable the experimental Wayland backend (assuming the desktop session runs under a Wayland):

```sh
flatpak override --user --env=MOZ_ENABLE_WAYLAND=1 org.mozilla.Thunderbird
```