# LINKS

- https://blog.mozilla.org/attack-and-defense/2021/04/27/examining-javascript-inter-process-communication-in-firefox/
- https://support.mozilla.org/en-US/kb/install-firefox-linux#w_local-firefox-installation-in-users-account
- vulnerabilities
  https://www.cvedetails.com/product/3264/Mozilla-Firefox.html?vendor_id=452
  https://www.mozilla.org/en-US/security/known-vulnerabilities/firefox/
- languages used
  https://4e6.github.io/firefox-lang-stats/
- What is a Browser Security Sandbox?!
  https://www.youtube.com/watch?v=StQ_6juJlZY
- Schutzwerk Linux Container Series:
  https://www.schutzwerk.com/en/blog/linux-container-intro/
- Container Security Fundamentals:
  https://www.youtube.com/watch?v=lBIvPys7L8w

# SANDBOX

Firefox does not use SUID sandbox. If user-namespaces are not available, Firefox only uses seccomp-bpf. This means that the sandbox will provide less protection as a part of it will be unusable.
## namespaces

```sh
# flatpak firefox open with 5 tabs
├─4026532682     user       16  8110 frida bwrap --args 43 -- firefox
│ ├─4026532683   mnt        16  8110 frida bwrap --args 43 -- firefox
│ └─4026532684   pid        16  8110 frida bwrap --args 43 -- firefox
```

## architecture
### content processes

New sandbox architecture: Fission
Each website is divided into separate processes (similar in Chromium)

![[Pasted image 20250331122246.png]]

### browser process vs render process

![[Pasted image 20250331122409.png]]
# FLATPAK

- https://arnaudr.io/2024/04/03/firefox-moving-from-the-debian-package-to-the-flatpak-app-long-term/
- https://blog.scottlowe.org/2022/06/01/making-flatpak-firefox-use-private-browsing-by-default/

```sh
mkdir .var/app/org.mozilla.firefox
# rm -fr ~/.var/app/org.mozilla.firefox/.mozilla/firefox/
cp -a ~/.mozilla/firefox/ ~/.var/app/org.mozilla.firefox/.mozilla/

# To avoid confusing myself, it's also a good idea to rename the old data directory:
mv ~/.mozilla/firefox ~/.mozilla/firefox.old.$(date --iso-8601=date)
```