# LINKS

* https://blog.cloudflare.com/sandboxing-in-linux-with-zero-lines-of-code/
* https://www.kuketz-forum.de/t/diskussion-flatpak-browser-sandbox-namespaces-bubblewrap/6408/12
* https://github.com/netblue30/firejail/wiki/Frequently-Asked-Questions#how-does-it-compare-with-docker-lxc-nspawn-bubblewrap
- https://clinta.github.io/vms-containers-and-docker/
- FOSDEM:  Do Linux Distributions Still Matter with Containers? 
  https://www.youtube.com/watch?v=3I713hTcPJU
- What is browser sandboxing? How to escape the sandbox?
  https://misile00.github.io/notes/Browser-Sandboxing
- Comparing Sandboxing Tools:
  https://hkubota.wordpress.com/2020/12/31/comparing-sandboxing-tools/
- Rory McCune
    - Container security fundamentals:
      https://securitylabs.datadoghq.com/articles/container-security-fundamentals-part-1/
    - Container Security Fundamentals - Linux Capabilities (Part 1)
      https://www.youtube.com/watch?v=lBIvPys7L8w
    - Container Security Fundamentals - Linux Namespaces (Part 4): The User Namespace:
      https://www.youtube.com/watch?v=lmig6_Y_dF4

# BASICS

Ist die Rede von **Linux-Containern**, meinen die Leute in der Regel die Kombination aus Namespaces und Cgroups.

# SANDBOX



# VS
## firejail vs bubblewrap

- https://github.com/netblue30/firejail/wiki/Frequently-Asked-Questions#how-does-it-compare-with-docker-lxc-nspawn-bubblewrap

Comparison of Firejail features vs. bubblewrap:

- very detailed and easy to use profiles for hundreds of applications, especially highly important browsers, messaging apps and media players
- automatic profile generation with `firejail --build`
- automatic desktop integration with `firecfg`
- more extensive network support including firewalling, protocol filtering, tunneling, DNS
- AppArmor and SELinux support
- D-bus filtering
- AppImage support
- resource limits (`rlimit-*=`)
- X11 sandboxing
- construct new file systems easily with `private-bin`, `private-etc`, `private-lib` etc.
- easy to use flags like `novideo`, `no3d`, `nodvd`, `notv`, `nogroups`, `noroot`, `nou2f`, `noexec` mount flag
- easier seccomp filtering (compared to loading cBPF programs) with deny-listing and allow-listing, 32-bit support, `memory-deny-write-execute`, `protocol`

# ESCAPES

Container Escape: All You Need is Cap:
  https://www.cybereason.com/blog/container-escape-all-you-need-is-cap-capabilities

## fs write permissions

With **write-permissions** write code in `~/.profile` or a desktop entry to `~/.config/autostart/` to have it auto-started on your next login. Not only will it run outside of any container, it will even persist after the app is uninstalled.

Suppose libjpeg has a zero-day remote code execution vulnerability. You open a seemingly innocuous JPEG in Flatpak GIMP and it drops its payload set to autostart in your home folder.