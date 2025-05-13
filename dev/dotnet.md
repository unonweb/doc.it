---
title: .NET
---

General
------------

* .Net framework applications are *multi-platform* applications
* der Programmcode verschiedener Programmiersprachen wird mittels eines Compilers in eine Zwischensprache (*Microsoft Intermediate Language (MSIL)*) übersetzt, die für alle Programmiersprachen gleich ist. Diese Zwischensprache ist plattformneutral.
* Durch das Konzept der Zwischensprache sind alle .NET-Sprachen gleichwertig, auch in Bezug auf die Ausführungsgeschwindigkeit.
* Der Code der Zwischensprache wird bei der Programmausführung nicht interpretiert, sondern durch einen *Just-In-Time-Compiler* in den Maschinencode des entsprechenden Prozessors übersetzt.

#### Dynamic Language Runtime
Die Besonderheit einer Sprache wie Visual C# ist, dass alle Datentypen bereits zur Übersetzungszeit bekannt sein müssen. Die Einbindung von sogenannten dynamischen Sprachen wie z. B. *JavaScript* fällt dadurch schwer (z.B. wenn Bibliotheken, die bereits in JavaScript vorliegen und von Visual C# aus genutzt werden sollen). Bereits mit dem .NET Framework 4.0 wurde die Dynamic Language Runtime eingeführt, die eine bessere Verwendung und Unterstützung solcher Sprachen ermöglicht.

### .NET 5.0

* November 2020
* gemeinsamer Nachfolger der drei bisher getrennten .NET-Varianten *.NET Framework*, *.NET Core* und *Mono*.

#### Anwendungen
* *ASP.NET* Core 5.0-basierte Webanwendungen mit Server-Side-Rendering oder Single-Page-Apps mit Client-Side-Rendering (mit Blazor)
* *Webservices* (REST-basierte WebAPIs und Google RPC)
* *Konsolenanwendungen*
* *Hintergrunddienste*
* *Desktop-Anwendungen* mit Windows Forms und Windows Presentation Foundation (WPF) für Windows ab Version 7
* *Windows Universal Apps* und Desktop-Anwendungen für Windows 10 mit der Windows UI Library 3 (Erscheinungstermin geplant für Frühjahr 2021)

CLI
-------

`dotnet`
* `new <template>`                  # Create a new .NET project or file.
  * calls the template engine to create the artifacts on disk based on the specified template and options.
  * templates:
    * `console`                     # Console Application
  * `--help`
  * `-o`|`--output`                 # Location to place the generated output.
  * `dotnet new console -o myapp`   # creates a new console app in a directory named myapp