---
title: Computer Arithmetik
tags: ['math', 'ausbildung']
---

Rechenarten
===============

Es gibt in der Computer Hardware keine Bausteine, die Subtrahieren. Es gibt nur die Addition (und die Komplementbildung).
Subtrahiert wird eine Zahl, indem man das zugehörige Komplement addiert.

1. Zweierkomplement vom Subtrahend bilden
2. addieren
3. letzten Übertrag wegwerfen



Datentypen
=============

Char
---------

* Größe 8 Bits = 1 Byte
* 

### Zeichenkodierung / Character encoding

[Wiki: Zeichenkodierung]("https://de.wikipedia.org/wiki/Zeichenkodierung")

* ASCII (American Standard Code for Information Interchange)
  * 7-Bit-Zeichenkodierung (= 128 verschiedene Zustände)
  * definiert 128 Zeichen, bestehend aus 33 nicht druckbaren sowie 95 druckbaren Zeichen
* Die meisten späteren Zeichenkodierungen sind so entworfen, dass sie für Zeichen zwischen 0 … 127 den gleichen Code verwenden wie ASCII und den Bereich über 127 für weitere Zeichen benutzen. 

Literal: In computer science, a literal is *a notation for representing a fixed value in source code*.

Integer
-------------

* ganze Zahl (von lateinisch *numerus integer*)
* *signed* = mit Vorzeichen
* *unsigned* = ohne Vorzeichen
* Größe: 


### Komplement

* Dezimale Äquivalente zum Zweier- und Einerkomplement im Binärsystem wären Zehner- und Neunerkomplementzahlen.

#### Zweierkomplement

1. Zahl invertieren (Einerkomplement bilden): `1 = 0001 --> 1110`
2. 1 dazu addieren: --> `1111`

Bei b-Komplementzahlen (*Zweierkomplementzahlen*) wird genau die halbe Teilmenge der Zahlen mit großem Betrag als negative Zahlen interpretiert, ohne dass die Arithmetik positiver Zahlen wesentlich geändert wird. Das führt zu einfachen Schaltungen und zu einer einfachen Regel für Vorzeichenänderungen (ziffernweises b-Komplement und anschließende Erhöhung der Zahl).

Nachteil von b-Komplementzahlen ist, dass der kleinste negative Wert kein positives Gegenstück in der Darstellung hat (asymmetrischer Wertebereich). 


#### Arithmetischer Überlauf
Wird einer Integer-Variable ein Wert außerhalb ihres Wertebereiches zugewiesen, führt dies zu einem arithmetischen Überlauf. So wird z. B. bei einer vorzeichenlosen 8-Bit-Integer-Variablen aus 255+1 der Wert 0; bei einer vorzeichenbehafteten im Zweierkomplement hingegen aus 127+1 der Wert −128. 


Float
----------

1. Festkommazahl: Komma steht immer an derselben Position
2. Fließkommazahl: Komma fließt immer hinter die erste von Null verschiedene Ziffer



