Batch Programmierung
========================

Mit Batch Dateien können einfache Abläufe in Microsoft Windows automatisiert werden. Batch Dateien verwenden die Befehle der Eingabeaufforderung, im einfachsten Fall können diese cmd-Befehle in einer einfachen Textdatei Zeile für Zeile eingetragen werden, es wird dann ein Befehl nach dem anderen ausgeführt.

Windows Batch stammen aus dem MS-DOS Zeitalter. Zu dieser Zeit hatten Batch Dateien die Dateiendung **.bat**. Seit Windows 2000 wird die Endung **.cmd** verwendet. Als Nachfolger von Windows Batch gilt die Microsoft PowerShell.


Keywords
----------------

#### if

`if exist c:\temp\my.log echo.>c:\temp\my.log`  
`IF "%<VARIABLE>%" == "<value>" GOTO <sprungmarke>`

#### >, >>
`echo Die Datei wurde überschrieben! > %homepath%\Desktop\Beispiel.bat`  
`echo Dies wurde an die Datei angehängt! >> %homepath%\Desktop\Beispiel.bat`  

### attrib
Ein Kommandozeilenprogramm in DOS, OS/2 und Windows. Ermöglicht den Anwender, Verzeichnis- oder Dateiattribute zu ändern. 


Variablen
----------------

`SET Variable=hallo` # Variable setzen  
`echo %Variable%` # Variable verwenden  

### Systemvariablen

`%USERNAME%`        # Name des Windows-Benutzers  
`%COMPUTERNAME%`    # Name des Computers  
`%USERDOMAIN%`      # die Domäne des angemeldeten Benutzers  

#### Parameter

`%0`        # the command used to call the batch file (could be foo, ..\foo, c:\bats\foo.bat, etc.)
`%1`        # the first command line parameter,
`%2`        # is the second command line parameter, and so on till `%9` (and `SHIFT` can be used for those after the 9th).
`%~nx0`     # the actual name of the batch file, regardless of calling method (some-batch.bat)
`%~dp0`     # drive and path to the script (d:\scripts)
`%~dpnx0`   # is the fully qualified path name of the script (d:\scripts\some-batch.bat) 

`%0%`               # Name der Batch-Datei, die gerade ausgeführt wird  
`%RANDOM%`          # eine Zufallszahlen (zwischen 0 und 32767)

#### Zeit & Datum
`echo %date%` oder `%time%` # aktuelles Datum oder die aktuelle Zeit ausgeben  
`echo Datum: %date%, Zeit: %time%`  

#### Dateisystem
`%ProgramFiles%`    # Installationspfad  
`%SystemRoot%`      # das Verzeichnis, in dem Windows installiert ist  
`%SystemDrive%`     # das Laufwerk, in dem Windows installiert ist  
`%windir%`          # Zugriff auf das Windows-Verzeichnis  
`%USERPROFILE%`     # Benutzerordner
`%temp%`           #  


Beispiele
----------------

`set /p AUSWAHL=Soll das Temp-Verzeichnis geleert werden? (j/n).` # Die Eingabe des Benutzers wird in der Variablen AUSWAHL gespeichert.  

```batch
IF %AUSWAHL% == j (
    del /f /s /q %temp%\*.*
    ) ELSE (
    echo Temp-Verzeichnis wurde nicht geleert
)
```
