# DAEMON

* https://www.freedesktop.org/software/systemd/man/latest/resolved.conf.html
* FOSDEM: How and why (not) to use the 127.0.0.53 nameserver, systemd-resolved
  https://www.youtube.com/watch?v=DtFjrJdnWAU
* stub-resolver
* Ubuntu

```sh
man systemd-resolved
```

```sh
systemctl status systemd-resolved.service
```

```sh
# use systemd-resolved
systemctl enable --now systemd-resolved.service
ln -sf /etc/resolv.conf /run/systemd/resolve/resolv.conf

# nano /etc/resolv.conf
nameserver 127.0.0.53 # points to the systemd-resolved stub listener
options edns0 trust-ad
search fritz.box
```

# CONFIG

```sh
man resolved.conf
```

```sh
# sudo xed /etc/systemd/resolved.conf
[Resolve]
DNSStubListener=no # disables the sub listener at 127.0.0.53:53
```

```sh
sudo systemctl restart systemd-resolved.service
```

# HOW TO
### use a specific DNS nameserver for a given domain

* Without the tilde, your resolvers are set for every DNS lookup. That's a pretty big hammer.
* With the tilde, the configured DNS resolvers are used for that domain. Other domains use default resolvers.

* `Domains=lem frida` # Any domains not prefixed with "~" are used as search suffixes when resolving single-label hostnames (domain names which contain no dot), in order to qualify them into fully-qualified domain names (FQDNs). These "search domains" are strictly processed in the order they are specified in, until the name with the suffix appendedis found. For compatibility reasons, if this setting is not specified, the search domains listed in /etc/resolv.conf with the search keyword are used instead, if that file exists and any domains are configured in it.
* `Domains=~lem ~frida` The domains prefixed with "`~`" are called "route-only domains". All domains listed here (*both search domains and route-only domains* after removing the        "`~`" prefix) define a search path that preferably directs DNS queries to this interface. This search path has an effect only when suitable per-link DNS servers are known. Such servers may be defined through the `DNS=` setting (see above) and dynamically at run        time, for example from DHCP leases. If no per-link DNS servers are known, route-only domains have no effect.

# RESOLVECTL

* https://www.freedesktop.org/software/systemd/man/latest/resolvectl.html#
* auch `resolvconf`
* may be used to resolve domain names, IPv4 and IPv6 addresses, DNS resource records and services with `systemd-resolved.service`

```sh
man resolvectl
```

```sh
resolvectl statistics
resolvectl query google.de
resolvectl status
resolvectl log-level <lvl>
resolvectl flush-caches # Flushes all DNS resource record caches the service maintains locally
```

```sh
journalctl -u systemd-resolved -f
```
