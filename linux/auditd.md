# LINKS

- https://linux-audit.com/linux-audit-framework/configuring-and-auditing-linux-systems-with-audit-daemon/
- https://izyknows.medium.com/linux-auditd-for-threat-detection-d06c8b941505
- https://izyknows.medium.com/linux-auditd-for-threat-hunting-part-2-c75500f591e8
- https://ivansalloum.com/auditing-linux-servers-with-auditd/
- https://github.com/Neo23x0/auditd

Videos

- Hilko Bengen: Linux Security Monitoring mit Audit Events: Schmerzen reduzieren 
  https://www.youtube.com/watch?v=G_v1UnWz1v4
- Detecting Exploits - OMIGod (Linux Logging with Auditd)
  https://www.youtube.com/watch?v=lc1i9h1GyMA
# LOG

```bash
less /var/log/audit/audit.log # standard log
```
# CONFIG

## audit.rules

- https://github.com/Neo23x0/auditd

```bash
man audit.rules
ll /usr/share/doc/auditd/examples/rules # predefined rules
sudo nano /etc/audit/rules.d/audit.rules # rules
```

```bash
sudo systemctl restart auditd # reload rules
```

```sh
-w /etc/passwd -p wa -k user-modify # user accounts
-a exit,always -S clock_settime -k changetime # change system time
```

### performance

- Regeln, die häufiger angewendet werden, sollten am Anfang der Konfigurationsdatei stehen, um die Verarbeitungsgeschwindigkeit zu erhöhen.
- 
## auditd.conf

```sh
sudo nano etc/audit/auditd.conf
```

```sh
log_file = /var/log/audit/audit.log
log_format = RAW
flush = INCREMENTAL
freq = 20
num_logs = 5
max_log_file = 8
max_log_file_action = ROTATE
```
# CLI
## auditctl

```bash
auditctl -l  # list active rules

auditctl -s # status
auditctl -e 1 # enable

auditctl -k <key>
auditctl -w <watchpath>
auditctl -p <permissions>
auditctl -S <syscall>

# examples
auditctl -w /etc/passwd -p rwxa -k passwd_watch # 
auditctl -a exit,always -F path=/etc/passwd -F perm=wa

```
## ausearch

```bash
ausearch -f <file>
ausearch -k <key>
ausearch -f /etc/passwd
ausearch -k passwd_watch
```

## aureport

```
sudo aureport -x
```