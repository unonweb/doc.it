
```sh
systemctl status logrotate.service
systemctl status logrotate.timer

systemctl cat logrotate.service
systemctl cat logrotate.timer
```
# CONFIG

```sh
apt list logrotate

sudo nano /etc/logrotate.conf
ll /etc/logrotate.d
cat /etc/logrotate.d/* | less
```

## ex

```sh
/var/log/nginx/*log {
	daily
	missingok
	rotate 3
	notifempty
	dateext
	dateformat -%Y-%m-%d
	create 640 http log
	compress
	sharedscripts
	postrotate
		test ! -r /run/nginx.pid || kill -USR1 `cat /run/nginx.pid`
	endscript
}
```

- `/var/log/nginx/log` 
  Das Verzeichnis, in dem logrotate bei der Ausführung suchen soll.
- **daily**
  Die Logs sollen täglich verarbeitet werden, Alternativen: hourly, weekly, monthly
- **missingok**
  Sollte das Logfile nicht vorhanden sein, wird keine Fehlermeldung erzeugt und mit der Verarbeitung fortgefahren.
- **rotate 3** 
  Bedeutet, dass immer drei rotierte Dateien aufgehoben werden. Sollte ein viertes dazu kommen, wird das älteste gelöscht.
- **notifempty**
  Mit einem leerem Log soll nichts passieren.
- **dateext**
  Ermöglicht das Hinzufügen eines Datums nach dem rotieren.
- **dateformat**
  Gibt den String an, der wegen dateext hinzugefügt werden soll.
- **create 640 http log**
  Erzeugt direkt nach dem Löschen eine neue Log Datei nach dem Muster:
  `<mode> <owner> <group>`
- **compress**
  Die rotierten Logs werden mit gzip komprimiert.
- **sharedscripts**
  Sorgt dafür, dass das Skript nach dem rotieren nur einmal ausgeführt wird.
- **postrotate / endscript**
  Der Bereich dazwischen wird nach dem rotieren als shell Skript ausgeführt.