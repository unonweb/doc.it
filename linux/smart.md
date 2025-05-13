# SMART

**Self-Monitoring, Analysis and Reporting Technology** 

Technologie mit der moderne Festplatten wichtige Parameter von sich selbst überwachen und aufzeichnen

# smartctl

```sh
sudo apt install smartmontools
```

## scan

```sh
smartctl --scan # Scans for devices and prints each device name, device type and protocol ([ATA] or [SCSI]) info
```

## read

```sh
sudo smartctl --all /dev/sda # Prints all SMART information about the disk
sudo smartctl --xall /dev/sda # Prints all SMART and non-SMART information about the device

# filter
sudo smartctl -i /dev/sdc # print only info about device
```

```sh
sudo smartctl --log error /dev/sdb # show error log
sudo smartctl --log selftest /dev/sdb # show selftest log
```

## test

### type

* **Long**: Wurde für den abschließenden Test in der Fertigung konzipiert und ist bis auf 2 Unterschiede gleich zum Short Test. Kann dazu verwendet werden um die Ergebnisse des Short Tests zu bestätigen.
  * keine Zeitbeschränkung 
  * im Read/Verify Segment wird die gesamte Festplatte überprüft und nicht nur ein Teil.

### mode

* **Background Mode**: Priorität geringer. Das bedeutet das weiterhin normale Instruktionen von der Festplatte verarbeitet werden. Falls die Festplatte ausgelastet ist wird der Test pausiert und bei geringerer Last wieder fortgesetzt, so kommt es zu **keiner Unterbrechung des Bestriebs**.
* **Foreground Mode**: Alle Kommandos an die Festplatte werden während des Tests mit dem Status "CHECK CONDITION" beantwortet, daher ist dieser Modus nur zu empfehlen wenn die Festplatte nicht verwendet  wird. **Grundsätzlich ist der Background Mode zu bevorzugen.**

Um die Tests im Foreground Mode durchzuführen muss "-C" zum Befehl hinzugefügt werden.

```sh
sudo smartctl -t short -C /dev/sdc
```

### run

```sh
sudo smartctl -c /dev/sdc
smartctl -t
smartctl --test long|short|conveyance|select /dev/sda
# long selftest; Dauer > 2h testet jeden einzelnen Block (Oberflächenanalyse)
# short selftest; Dauer < 5min
```

# update-smart-drivedb

aktualisiert die Datenbank, die smartctl bei der Identifizierung der Speichermedien befragt; relevant insbesondere bei aktuellen SSDs.

# smartd

monitors certain disk drives and attempts to predict when they will fail

```sh
xed /etc/default/smartmontools
```

```sh
enable_smart= # legt das zu überwachende Gerät fest
start_smartd= # aktiviert den automatischen Start
smartd_opts="--interval=3600" # sorgt für eine Überprüfung durch smartd alle 3600 Sekunden
```