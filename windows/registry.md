---
title: Windows Registry
---

Registry
=========

* die zentrale hierarchische Konfigurationsdatenbank  
* Bevor sich in Windows das Konzept der Registry durchgesetzt hatte, wurden Einstellungen in Konfigurationsdateien (INI-Dateien) separat für jedes einzelne Programm in dessen Verzeichnis gespeichert.  

#### Vorteile
* wird in einem binären Format gespeichert, sodass ihre Inhalte direkt und ohne Konvertierung weiterverarbeitet werden können
* Über Gruppenrichtlinien können mehrere Arbeitsplatzrechner zentral und auf einmal gesteuert werden, denn auf die Daten der Registry kann auch über ein Netzwerk zugegriffen werden, da die Pfade zu den Werten standardisiert sind: Ein Programm muss nicht wissen, wo eine bestimmte Datei liegt; es spricht nur die Standard-API an, welche den Registrywert in dem Schlüssel ausliest oder schreibt. 

#### Nachteile
* Die zentralisierte und hierarchische Struktur kann leicht zu einem Single Point of Failure führen, wenn hierarchisch übergeordnete Schlüssel fehlerhaft sind. Einstellungen, die keine oder eine flache hierarchische Struktur, wie Konfigurationsdateien, aufweisen, funktionieren meist auch dann noch, wenn einzelne Werte fehlerhaft oder einzelne Dateinamen falsch sind.

Aufbau
------------------

Die Registrierungsdatenbank besteht aus Schlüsseln (**keys**) und Einträgen (**entries**). Ein Schlüssel ist dabei ein Behälter für Einträge und weitere Unterschlüssel, ähnlich einem Ordner auf Dateiebene.

#### Haupt-/Wurzelschlüssel
, unter HKEY_CURRENT_USER (HKCU) solche, die nur das aktuelle Benutzerkonto betreffen.

* **HKEY_LOCAL_MACHINE (HKLM)** # systemweite Einstellungen, d.h. alle am System angemeldeten Benutzerkonten betreffen.
* **HKEY_USERS** # benutzerspezifische Einstellungen; für jeden Benutzer ist dort ein eigener Unterschlüssel angelegt, benannt nach der SID des jeweiligen Benutzerkontos.
* **HKEY_CLASSES_ROOT (HKCR)**: speichert Windows Informationen über Dateitypen und COM-Klassen, also etwa was passiert, wenn Sie eine bestimmte Datei im Explorer doppelklicken, oder was nötig ist, um eine Excel-Tabelle in ein Word-Dokument einzubetten. Die Daten unter HKCR stammen in Wahrheit aus den Schlüsseln unter HKLM\Software\Classes und HKCU\Software\Classes; existieren in beiden Ästen gleichnamige Werte, gewinnt HKCU.


#### Dateien der Registry (Hives)

Die Registrierungsdatenbank wird über mehrere Dateien verteilt gespeichert, die in verschiedenen Verzeichnissen des Rechners abgelegt sind. Somit wird die Registry in mehrere Teilabschnitte unterteilt, welche auch als Hives (englisch für Bienenstöcke) bezeichnet werden. Ein Hive ist dabei nicht zwangsweise mit einem Wurzelschlüssel identisch. So gibt es Wurzelschlüssel, die aus mehreren einzelnen Hives bestehen. Des Weiteren können Wurzelschlüssel auch nur virtuell sein, also einen Link auf einen anderen Teil der Registrierungsdatenbank darstellen. 

## Powershell

Man kann auf die Registry direkt über die Konsole oder durch ein Shellskript wie auf ein herkömmliches Laufwerk zugreifen.  
`cd HKLM:` # Wechsel auf den Hauptschlüssel HKEY_LOCAL_MACHINE  
`Get-ItemProperty .` # zeigt alle Eigenschaften (Registry-Einträge), die für den aktuellen Registryschlüssel gespeichert sind  