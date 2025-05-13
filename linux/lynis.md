# lynis

* https://github.com/CISOfy/lynis/releases

```bash
lynis show commands
```

```bash
lynis audit system
```

## log

```sh
less /var/log/lynis.log # Test and debug information
/var/log/lynis-report.dat # Report data
```

## report

### suggestions

Towards the bottom of the report, Lynis offers suggestions based on the report's findings. Each suggestion is followed by a **TEST-ID**:

```text
 Suggestions (47):
  ----------------------------
  * If not required, consider explicit disabling of core dump in /etc/security/limits.conf file [KRNL-5820] 
      https://cisofy.com/lynis/controls/KRNL-5820/
```

```bash
lynis show details KRNL-5820
```