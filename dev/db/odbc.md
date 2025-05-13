---
title: ODBC
----


ODBC
==========

**Open Database Connectivity**; eine standardisierte, offene Schnittstelle für den Zugriff auf unterschiedliche Datenbankmanagementsysteme. Über ODBC-Treiber können Anwendungen direkt Anweisungen an Datenbanken erteilen oder Abfragen ausführen.  

https://www.bigdata-insider.de/was-ist-odbc-a-626950/


* ursprünglich von Microsoft entwickelt; hat sich mittlerweile als eine Art Standard für den Zugriff von Anwendungen auf unterschiedliche Datenbanken etabliert und ist neben Windows auch für Betriebssysteme wie Unix oder macOS verfügbar.
* ODBC-Anweisungen können an Datenbanken wie *dBase*, *Access* und *DB2* oder direkt an Excel-Dateien gesendet werden. 

Für den Zugriff auf die verschiedenen Datenbanken benötigt ODBC spezielle Treiber. Dank der Nutzung von SQL können die Anwendungen mit den Datenbanken kommunizieren, ohne die proprietären Protokolle oder Schnittstellen der verschiedenen Datenbankmanagementsysteme zu kennen. Die SQL-Befehle der Anwendungen wandelt ODBC in die Sprache und Formate der Datenbanken um. Der eigentliche Zugriff auf die Daten erfolgt niemals direkt auf die Datenbank oder die Tabelle, sondern immer über die zugehörige ODBC-Komponente. Open Database Connectivity erlaubt sowohl den Zugriff auf lokale als auch auf entfernte Datenbanken über Netzwerkverbindungen. 


Aufbau und Funktionsweise
-----------------------------------

Möchte eine Anwendung auf eine Datenbank als Datenquelle über ODBC zugreifen, sind vier Komponenten beteiligt. Diese Komponenten sind:

* die Anwendung
* der ODBC-Treiber-Manager
* der ODBC-Treiber
* die Datenquelle (Datenbank)

In der Datenquelle sind die für die jeweilige Anwendung relevanten Daten gespeichert. Die Datenquelle kann sowohl eine Datenbank als auch eine Excel-Tabelle oder eine Text-Datei sein. Die Anwendung kommuniziert mit der Datenquelle, indem sie zunächst den Verbindungsaufbau initiiert. Hierfür wendet sie sich zunächst an den Treiber-Manager und benennt über den **Data Source Name** (DSN) die Datenquelle. 

