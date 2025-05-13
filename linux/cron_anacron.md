**daemon to execute scheduled commands**; wakes up every minute, examining all stored **crontabs**

# ANACRON

* Anacron ist (im Gegensatz zu cron) **für Systeme ausgelegt, die nicht dauerhaft laufen**
* Anacron arbeitet im Gegensatz zu cron nicht mit definierten Zeitpunkten sondern mit **Zeitstempeln**.
* **Stündliche Aufgaben** werden jedoch per cron ausgeführt, was auch einen installierten cron Daemon auf dem System bedingt

Eine Aufgabe wird ausgeführt und anschließend der Zeitstempel der  letzten Durchführung im Format YYYYMMDD gespeichert. Wenn dieser  Zeitstempel einer täglichen Aufgabe auf den Vortag datiert ist, führt  Anacron diese Aufgabe wieder am nächsten Tag aus und setzt den  Zeitstempel erneut.

## config

```sh
sudo nano /etc/anacrontab # config
ll /var/spool/anacron/cron.daily # timestamps
ll /etc/cron.daily
ll /etc/cron.weekly
ll /etc/cron.monthly
```

## syntax

```sh
1     2     3     L    
|     |     |     |
|     |     |     +----- Befehl der ausgeführt werden soll
|     |     |     
|     |     +----- job-identifier (Datei für den Zeitstempel)
|     +----- delay (Zeitverzögerung nach Systemstart in Minuten)
+----- period (Zeit in Tagen)
```

## defaults

(debian, ubuntu, mint)

```sh
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
HOME=/root
LOGNAME=root

# These replace cron's entries
1	5	cron.daily	run-parts --report /etc/cron.daily
7	10	cron.weekly	run-parts --report /etc/cron.weekly
@monthly	15	cron.monthly	run-parts --report /etc/cron.monthly
```

# LINKS

- https://crontab.guru/

# DAEMON

```sh
systemctl status cron.service
sudo systemctl edit cron.service #
sudo systemctl edit --full cron.service # specify startup options here!

# EnvironmentFile=-/etc/default/cron
# ExecStart=/usr/sbin/cron -f $EXTRA_OPTS
```


```sh
cron -L <loglevel> # tells cron what to log about jobs (errors are logged regardless of this value) as the sum of the following values:

1 # will log the start of all cron jobs (default)
2 # will log the end of all cron jobs
4 # will log all failed jobs (exit status != 0)
8 # will log the process number of all cron jobs
15 # selects all options.

cron -f # stay in foreground mode, don't daemonize.
```


```
sudo nano /etc/default/cron
```

# CRONTABS

- jede crontab muss mit einem Zeilenumbruch abgeschlossen werden!
## user

- user specific crontabs enthalten (logischerweise) keine _User_-Spalte!

```sh
sudo ll /var/spool/cron/crontabs
```

```sh
crontab -e # edit the current crontab using the editor
crontab -l # listet den Inhalt der crontab auf
crontab -r # removes the current crontab
crontab -lu <user> # listet die crontab von _user_
crontab -ru <user> # löscht die crontab von _user_
```

## system

```sh
sudo nano /etc/crontab # 
ll /etc/cron.hourly
ll /etc/cron.daily
ll /etc/cron.weekly
ll /etc/cron.monthly
```

Die Dateien in diesen Ordnern werden mittels `run-parts` gestartet und unterliegen deshalb Einschränkungen:

- Sie müssen ausführbar sein.
- Der Dateiname darf nur große und kleine Buchstaben, Ziffern, Unterstriche und  Bindestriche enthalten; andernfalls wird die Datei ignoriert.

```sh
/etc/cron.d/ # Skripte, die nicht an das hourly/monthly/.. -Schema passen
```
## syntax

| 1st    | 2nd  | 3rd      | 4th   | 5th      |
| ------ | ---- | -------- | ----- | -------- |
| minute | hour | day date | month | day name |
| 0-59   | 0-23 | 1-31     | 1-12  | 0-6      |
- Day Names 0-6 begin with Sunday

Instead of the first five fields, one of eight special strings may appear:

```sh
@reboot # run once, at startup (the time when the cron daemon startup)
@yearly # run once a year: 0 0 1 1 *
@monthly # run once a month: 0 0 1 * *
@weekly # run once a week: 0 0 * * 0
@daily # run once a day: 0 0 * * *
@hourly # run once an hour: 0 * * * *
```


```sh
0 0 * * 0 # Run at 00:00 [midnight] every Sunday
```

```sh
*/5 	3-6 	* 	*/2 	0,6 	echo “Linux is Cool!” >> ~/crontab_log.txt
```

| Field    | Value | Meaning                                           |
| -------- | ----- | ------------------------------------------------- |
| Day Name | 0,6   | Saturday and Sunday                               |
| Month    | */2   | Every month that is divisible by 2 (even) months. |
| Day Date | *     | Every Date                                        |
| Hour     | 3-6   | Between 3 and 6 AM                                |
| Minutes  | */5   | Every 5 Minutes                                   |
## command output

alle **Ausgaben** von Kommandos, die durch cron ausgeführt werden, werden per default an die interne E-Mail-Adresse des users geschickt (Postfach: /var/mail/_user_); es sei denn man leitet sie explizit um.

Mit der Variablen MAILTO= kann eine einheitliche E-Mail-Adresse festgelegt werden.

```sh
0 * * * *  echo "Linux is Cool!" >> ~/crontab_log.txt # log to file
```

```sh
0 0 * * * echo "Hey" > /dev/null 2>&1 # bypass
```