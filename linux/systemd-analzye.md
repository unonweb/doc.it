
* https://www.freedesktop.org/software/systemd/man/249/systemd-analyze.html#

```bash
systemd-analyze get-log-level
systemd-analyze security
```
## systemd-analyze security

* https://www.freedesktop.org/software/systemd/man/249/systemd-analyze.html#systemd-analyze%20security%20%5BUNIT...%5D
* exposure level: 0 - 10
  High exposure levels indicate very little applied sandboxing. 
  Low exposure levels indicate tight sandboxing and strongest security restrictions. 
* only analyzes the per-service security features systemd itself implements