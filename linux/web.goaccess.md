- quickly analyze and **view web server statistics** in real time

# CLI

```bash
goaccess -f # log file path
goaccess -a # enables all recorded statistics for each host agent parsed
goaccess  -c or --config-dialog # prompt log/date configuration window on program start.
goaccess --log-format='%h, %l %u %^[%d:%t %^] %m %U %H %s %b %q %R %L' #
goaccess --date-format='%d/%b/%Y' # 
goaccess --date-spec=hr 
goaccess --time-format='%H:%M:%S' #
```

# CONFIG

```bash
sudo nano /etc/goaccess.conf # 
```