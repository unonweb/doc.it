- https://www.freedesktop.org/software/systemd/man/latest/systemd-sysext.html
- Testing my System Code in /usr/ Without Modifying /usr/
  https://0pointer.net/blog/testing-my-system-code-in-usr-without-modifying-usr.html

activates/deactivates system extension images. System extension images may – dynamically at runtime — extend the `/usr/` and `/opt/` directory hierarchies with additional files. This is particularly useful on immutable system images where a `/usr/` and/or `/opt/` hierarchy residing on a read-only file system shall be extended temporarily at runtime without making any persistent modifications.