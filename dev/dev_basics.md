---
title: Dev basics
---

MISC
=============================================




STYLES & PATTERNS & PARADIGMS
==================================

## Paradigms

* *Imperatives*: Das Programm besteht aus einer Folge von Anweisungen, die streng sequenziell abgearbeitet werden. Das Konzept des imperativen Programmierparadigmas beruht auf Funktionen und Prozeduren zur Abbildung der Funktionalität und Wiederverwendung von Quellcode. Der bekannteste Vertreter ist die Programmiersprache *C*.
* *Objektbasiertes*: Diese Programmiersprachen kennen Objekte zur Kapselung von Daten und Funktionalität. Weitergehende Konzepte wie beispielsweise Vererbung oder die Abbildung von Beziehungen zwischen Objekten werden jedoch nicht angeboten. Objektbasierte Sprachen können damit als Vorstufe der objektorientierten Programmierung aufgefasst werden. Ein Beispiel ist die Script-Sprache *Microsoft Powershell*.
* *Objektorientiertes*: Programmiersprachen dieser Gattung erweitern das objektbasierte Programmierparadigma um die typischen Konzepte der Objektorientierung wie Vererbung und Polymorphie. Moderne und häufig eingesetzte Vertreter sind *Java* und *C#*.
* *Funktionales*: Ein funktionales Programm besteht nur aus einer Reihe von Funktionsaufrufen. Eigenständige Wertzuweisungen existieren nicht; alle Elemente können als Funktionen aufgefasst werden. Einsatzgebiete sind Anwendungen der künstlichen Intelligenz, Compilerbau und Computeralgebrasysteme. Funktionale Sprachen beruhen auf dem so genannten Lambda-Kalkül. Beispiele funktionaler Programmiersprachen sind *Lisp*, *Haskell* und – mit zunehmender Bedeutung – die Sprache *F#*.
* *Logisches*: Im Mittelpunkt steht der Aufbau einer Datenbasis, die aus Fakten und Regeln besteht. Fakten sind dabei wahre Aussagen im Sinne der Mathematik. Im Fokus steht die Problemformulierung, nicht der Lösungsalgorithmus. Die Sprache *Prolog* basiert auf dem logischen Paradigma.
* *Deklaratives*: Überbegriff für das funktionale und das logische Paradigma.

### Strukturierungsmöglichkeiten

Programmsysteme kann man auf zweierlei Weise strukturieren:

1. durch eine *funktionale Abstraktion*, d. h. in dem das gesamte Programm als ein großer Algorithmus aufgefasst wird, der mit Hilfe von schrittweiser Verfeinerung der Algorithmen bis auf elementare Anweisungen zerlegt wird. Prozeduren werden in Unterprozeduren mit Unterprozeduren mit Unterprozeduren (usw.) zerlegt. Dabei müssen dann die Daten immer wieder durchgereicht werden. Daten und Algorithmen werden getrennt behandelt. Das ist eine Methode, die bei größeren Programmsystemen mit erheblichen Problemen behaftet ist.

2. durch *Datenabstraktion*, d. h. die Datenstrukturen stehen im Mittelpunkt der Betrachtung und sie haben eigene Algorithmen zur Verfügung, die sie zur Erledigung ihrer Aufgaben und Änderung ihres Zustands brauchen. Diese Sichtweise ist Grundlage der objektorientierten Programmierung (OOP). Der entscheidende Unterschied liegt darin, dass bei OOP die Objekte "ein Gedächtnis" haben, also immer den Zustand ihrer Daten kennen.


Styles
---------------------------------------------------------------

```js
// imperative
let name = 'Guybrush';
let phrase = 'Hello, my name is ';
function sayHello() {
	console.log(phrase + name);
}
sayHello();

// functional
function sayHello(phrase, name) {
	console.log(phrase + name);
}
sayHello('Hello, my name is ', 'Guybrush');
```

### Functional programming

* pure functions: take input, compute it, return output
* given the same input, will always return the same output without side effects.
* no variables from from outside the function are involved within the function itself
* avoid side effects
* Works well in JavaScript is because it treats functions as values. Thus you can assign functions as values and pass them as parameters. You can even return another function as an output, and pass that to yet another function!

#### JS

most things will be *const* rather than *let* or *var*



### Imperative programming

* "first take this, then do that"
* modify in-place


Patterns
----------------------------------------------------------------------

### Input-calculate-Output:

the simplest overall program model. First obtain all the data you need (for instance by prompting the user for keyboard input). Calculate what you need from this data. Output the data (for instance to the screen with print functions).

### Repetitive patterns

all associated with loops. Loops are essential if the number of repetitions depends on dynamic data in the program.

* kopfgesteuerte Schleife:  `while <condition> ...`
* fußgesteuerte Schleife:   `do...while <condition>`
* exact repetition some number of times
* for-each loop
* successive modification loop
* accumulation loop

#### exact repetition
```js
// Exact repetition some number of times (n)
for (let i = 0; i < n; i++) {
    // actions to be repeated
}

// For-each loop (Do the same sort of thing for each item in a sequence)
for (item in array) {
    // actions to be done with each item
}
```

#### successive modification

Repeat a basic idea, but where the data involved each time changes via a pattern that is coded in the loop to convert the previous data into the data needed the next time through the loop:
```js
// initialize all variables that will be successively modified in the loop
// loop heading for the repetition:
	// actions to be in each loop with the current variable values
// modify the variable values to prepare for the next time through the loop
```

#### accumulation loop

A sequence of items need to be combined. This works where the accumulation of all the items can be approached incrementally, combining one after another with the accumulation so far


Functions
-----------------------------------------------------------------


### Command–query separation

a principle of imperative computer programming. 

It states that every method should *either be a command* that performs an action, *or a query* that returns data to the caller, but not both.  
In other words, *"Asking a question should not change the answer"*. More formally, methods should return a value only if they are referentially transparent and hence possess no side effects.

### Side effects

An operation, function or expression is said to have a side effect if it modifies some state variable value outside its local environment besides returning a value (the main effect) to the invoker of the operation.

* Imperative programming is commonly used to produce side effects, to update a system's state.
* In functional programming, side effects are rarely used.

### Pure Function

The definition of a pure function is:

1. The function always returns the same result if the same arguments are passed in. It *does not depend on any state or data*, change during a program’s execution. It must only depend on its input arguments.
2. The function does not produce any observable *side effects* such as network requests, input and output devices, or data mutation.


OBJEKTORIENTIERUNG
==============================================

### Prototypen

* *klassenlose*, *prototyporientierte* oder *instanzorientierte* Programmierung bezeichnet.

### Abstrakte Klassen

Eine abstrakte Klasse ist eine, von der selbst keine Exemplare abgeleitet werden (können). Sie dient im allgemeinen nur dazu, konkrete Unterklassen abzuleiten.

* Beispiel: Versicherungsverträge:  
  Es gibt keinen Versicherungsvertrag "an sich", aber Feuer-, Lebens-, Haftpflichtversicherungen. 

### Kapselung

Das Konzept der Kapselung in der objektorientierten Programmierung hat den Zweck, zwischen einer öffentliche API und der internen Implementierung zu unterscheiden.

In object-oriented programming, properties and methods are split into two groups:

* *Internal interface* – methods and properties, accessible from other methods of the class, but not from the outside.
* *External interface* – methods and properties, accessible also from outside the class.



### UML (Unified Modeling Language)

Sie bietet ein ganzes Spektrum an Diagrammtypen für die Visualisierung der unterschiedlichsten Sachverhalte und ist auf den objektorientierten Ansatz ausgerichtet.

* *Klassendiagramm*: 
  * beschreiben grafisch die Beziehungen (z.B. Vererbung) zwischen den Klassen
  * stellen eine statische Sichtweise auf die Anwendung dar

### Static methods

* are associated with a class, but not with specific instances of that class. 
* do not require an object of the class as an input argument.
* you can call static methods without creating an object of the class.

Statische Methoden werden ohne Instanzierung einer Klasse aufgerufen und sind über eine erzeugte Instanz nicht aufrufbar. Oft werden in statische Methoden für Hilfsfunktionen verwendet.


Beziehungen zwischen Klassen
---------------------------------------

Eine prinzipielle OO-Sichtweise beschreibt das Verhältnis zwischen zwei aktiven Objekten als eine typische *Client-Server* Situation. Wenn ein Objekt irgendeine Aktion nicht aus dem eigenen Verhaltensrepertoire erfüllen kann, dann fordert es als Client mit einer Botschaft von einem anderen Objekt (Server) einen Dienst oder eine Leistung an. Realisiert wird die Client-Server Beziehung über eine der Referenzbeziehungen.

Aus der Fülle der möglichen Beziehungssituationen sind folgende drei von besonderer Bedeutung: *Vererbung*, *Aggregation* und *Assoziation*.

### Vererbungsbeziehungen

* Spezialisierung / Generalisierung
* Unterklassen / Oberklasse
* Die Vererbungsbeziehung ist eine Spezialisierung vom Allgemeinen (Gemeinsamen) zum Besonderen  
  (wie Sie auch bei der wissenschaftlichen Klassifikation angewandt wird)
* Beispiel: In einem Betrieb gibt es verschiedene Arten von Mitarbeitern.  
  Alle drei haben die gleichen Personenattribute wie Name, Vorname usw., die sich quasi überdecken.  
  Man spricht daher auch von einer *Überdeckungsbeziehung*.  
  Ein Arbeiter ist eine Art von Mitarbeiter ('*IS-KIND-OF*' oder '*IS-A*'), der aber eine besondere Form der Lohnberechnung hat.
* *property shadowing*

#### Mehrfachvererbung

#### Prototypen vs Klassen

> Bei prototypischer Vererbung werden nur sich unterscheidende Eigenschaften beim abgeleiteten Objekt gesetzt.

Diese Art der Vererbung nennt man *Differential Inheritance*. Im Gegensatz zur klassenbasierten Vererbung werden beim abgeleiteten Objekt (der Instanz) keine Eigenschaften erzeugt. Die Instanz ist keine Kopie des Prototypen, die Instanz kann sogar leer sein. Erst wenn sich die Instanz vom Prototypen unterscheidet, wird bei der Instanz eine Eigenschaft angelegt, die einen anderen Wert als die des Prototypen besitzt. 

### Referenz- oder Nutzungsbeziehungen

#### Aggregation

* Sonderfall einer *Assoziation*.  
* Sie drückt ein starkes semantisches Verhältnis von zwei an sich selbständigen Objekten aus, von denen eines Teil des anderen ist ('*IS-PART-OF*').
* Beispiel: Der Motor ist Teil eines Autos  
  Motor als komplexes Objekt wird dem Autoobjekt hinzugefügt (aggregiert) und damit zu einem Attribut von TAuto.  
  Es wird daher als eingebundenes *Komponentenobjekt* bezeichnet.

#### Assoziation

* Sie drückt das Verhältnis von zwei völlig selbständigen Objekten aus, die auf der gleichen Abstraktionsebene stehen und eigentlich nichts miteinander zu tun haben, aber unter bestimmten Gesichtspunkten in eine lose Kennt-Beziehung ('*KNOWS*') gebracht werden können.