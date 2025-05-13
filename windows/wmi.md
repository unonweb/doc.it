---
title: WMI
---

Windows Management Instrumentation
=======================================

* Microsofts Implementierung des **Common Information Models** (CIM)
* CIM ist dabei das sogenannte Informationsmodell des standardisierten *Web Based Enterprise Managements* (WBEM) für Windows: Bei WBEM geht es darum, Standardfunktionalitäten zur Verfügung zu stellen, mit denen dann Rechner administriert werden können.
* WMI ermöglicht den schreibenden und lesenden Zugriff auf nahezu alle Einstellungen von Windows-Systemen.
* Zugriff per:
  * PowerShell
  * VBScript
  * WMIC (Windows Management Instrumentation Command Line)
  * WMIC (Windows Management Instrumentation Console) angesprochen.

WMI ist in einem dreischichtigen Modell aufgebaut: 
* *Provider*: Software-Agenten, die Informationen (beispielsweise über das Betriebssystem), Dienste, Programme oder Systemtreiber verarbeiten und an den CIM Objekt Manager weiterreichen
* *CIM Object Manager* (CIMOM): ein Systemdienst, der auch als *WinMgmt* bezeichnet wird. Provider verbinden sich mit dem CIMOM über COM-Schnittstellen.
* *Konsumenten* der WMI-Informationen

CIMOM speichert alle Informationen in strukturierte Klassen und ermöglicht einen gezielten Zugriff auf gewünschte Management-Informationen.
* Properties
* Methods

Events / Ereignisse: Meldungen, die ein „WMI-Konsument“ quasi abonnieren kann.

WMI classes
-----------------

* `ComputerSystem`
* `CPU`
* `Win32_Directory`
  * Methods
    * `Compress()` # der Befehl „Komprimieren“, den Anwender auch aus der Windows-Oberfläche selbst kennen.
* `Win32_Processor`
  * Properties
    * `LoadPercentage`
* `Printer`


Computerverwaltung \>> Dienste und Anwendungen \>> WMI-Steuerung \>> Eigenschaften

WMIC
----------

(Windows Management Instrumentation Command-Line)