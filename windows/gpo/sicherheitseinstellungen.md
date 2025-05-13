---
title: Sicherheitseinstellungen
---

Security Baselines
==========================


### Microsoft Security Compliance Toolkit
download, analyze, test, edit and store Microsoft-recommended security configuration baselines for Windows and other Microsoft products, while comparing them against other security configurations.   
[download]([https://link](https://www.microsoft.com/en-us/download/details.aspx?id=55319))

* Local Group Policy Object (**LGPO**): ein Befehlszeilendienstprogramm, das zum Automatisieren der Verwaltung von lokalen Gruppenrichtlinien entwickelt wurde

#### Policy Analyzer
Dienstprogramm zum Analysieren und vergleichen Sätze von Gruppenrichtlinienobjekten (GPOs). Einige Features sind:

* Markieren eines Satzes von Gruppenrichtlinien mit redundante Einstellungen oder interne Inkonsistenzen
* Markieren der Unterschiede zwischen Versionen oder Sätze von Gruppenrichtlinien
* compare GPOs against current local policy settings and against local registry settings
* export to Excel
* *Policy Definitions in*: `\\<server>\sysvol\<domain>` (enthält die Gruppenrichtlinien und Skripte für `<domain>`  
  z.B. `\\Evosrv-dc1\sysvol\evo-solutions.local\Policies`
* *Compare to Effective State*: die ausgewählten Baselines mit dem aktuellen Systemzustand vergleichen.
* Wenn die ausgewählten Baselines irgendwelche Benutzerkonfigurationseinstellungen enthalten, werden sie mit den Einstellungen des aktuellen Benutzers verglichen.
* Der effektive Zustand, der den Einstellungen der ausgewählten Baseline(s) entspricht, wird in einem neuen Richtlinienregelsatz gespeichert.

#### PolicyRule
* Mit PolicyRule-Dateien können *eigene Security Baselines festgelegt werden* und einfach auf einem System überprüft werden, ob die vorgegebenen Security Baselines eingehalten werden.
* der Policy Analyzer arbeitet mit PolicyRules-Dateien.
* Eine PolicyRules-Datei beinhaltet XML mit dem folgenden Aufbau.


SICHERHEITSEINSTELLUNGEN
================================================

* secpol.msc
* gpedit.msc: `Computer Configuration >> Windows Settings >> Security Settings`


Kontorichtlinien
-------------------------------------
Account Policies


### Kennwortrichtlinien

* *Kennwort muss Komplexitätsvoraussetzungen entsprechen*
* *Kennwortchronik erzwingen*
* *Minimale Kennwortlänge*


Windows Firewall mit erweiterter Sicherheit
---------------------------------------------


Netzwerklisten-Mananger-Richtlinien
---------------------------------------------------------------------
Network List Manager Policies


Richtlinien für öffentliche Schlüssel
----------------------------------------------------------------
Public Key Policies


Lokale Richtlinien / Sicherheitsoptionen
------------------------------------------------------------
Local Policies / Security Options


* *Interaktive Anmeldung: Kein STRG+ALT+ENTF erforderlich* 
  * deaktiviert  
    Wenn diese Richtlinie deaktiviert ist, muss jeder Benutzer vor der Anmeldung an Windows STRG+ALT+ENTF drücken. Sie können für erhöhte Sicherheit anfordern, dass Benutzer vor der Anmeldung die Tastenkombination STRG+ALT+ENTF drücken. Dies gewährleistet das Anzeigen der authentischen Windows-Anmeldeseite und schützt das System vor Programmen, die durch das Darstellen einer Anmeldung versuchen, Kennwortinformationen abzurufen.

### Benutzerkontensteuerung

* *Benutzerkontensteuerung: Alle Administratoren im Administratorgenehmigungsmodus ausführen*
* *Benutzerkontensteuerung: Anwendungsinstallationen erkennen und erhöhte Rechte anfordern*
* *Benutzerkontensteuerung: Bei Benutzeraufforderung nach erhöhten Rechten zum sicheren Desktop wechseln*
* *Benutzerkontensteuerung: Verhalten der Eingabeaufforderung für erhöhte Rechte für Administratoren im Administratorgenehmigungsmodus*
  * Eingabeaufforderung zu Anmeldeinformationen auf dem sicheren Desktop.
  * Eingabeaufforderung zur Zustimmung auf dem sicheren Desktop.

Lokale Richtlinien / Zuweisen von Benutzerrechten
------------------------------------------------------------
Local Policies / User Rights Assignment


* *Als Dienst anmelden* / *Log on as a service*
  * allows accounts to start network services or services that run continuously on a computer, even when no one is logged on to the console.
* *Log on as a batch job*
  * You should allow the computer to manage this user right automatically if you want to allow scheduled tasks to run for specific user accounts. If you do not want to use the Task Scheduler in this manner, configure the Log on as a batch job user right for only the Local Service account.
* *Lokal anmelden verweigern*
* *Zugriff vom Netzwerk auf diesen Computer verweigern*


Lokale Richtlinien / Überwachungsrichtlinie
------------------------------------------------------------



Anwendungssteuerungsrichtlinien
---------------------------------

### Applocker


Prevents malicious software (malware) and unsupported applications from affecting computers in your environment, and it prevents users in your organization from installing and using unauthorized applications.