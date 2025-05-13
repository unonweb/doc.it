# LINKS

- https://www.kuketz-forum.de/t/diskussion-flatpak-browser-sandbox-namespaces-bubblewrap/6408

# INFO
## suid or userns

**If bwrap is run with suid, the native sandbox of electron for example won't be able to run, since the use of suid is blocked inside a bwrap sandbox.** That means when running bwrap with suid, electron apps can only be run with the option --no-sandbox, disableing the native sandboxing features. **With unprivileged userns available both the bwrap sandbox and the native sandbox work fine.**