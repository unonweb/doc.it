Struktur
-----------

* Alle Methoden müssen innerhalb einer Klasse definiert werden.

```cs
    using System;
    namespace Learning;
    public class HelloWorld
    {
        public static void Main(string[] args)
        {
            Console.WriteLine ("Hello Mono World");
        }
    }
```
* `using System;`           # importiert den Namespace System. Das bewirkt, dass man auf die Klasse *Console* direkt zugreifen kann (anstatt über *System.Console*) 
* `namespace Learning;`     # 
* `public class HelloWorld` # Auf diese Klasse kann mit *Learning.HelloWorld* zugegriffen werden. 
* Die Methode *Main* ist eine spezielle Methode (Hauptprogramm) und stellt den Programmeintrittspunkt dar.


CONTROL FLOW
================

### Conditions

#### if else
* if there's a larger number of logical evaluations of a value better use `switch`
```cs
    if (i > j)
    {
        Console.WriteLine("i is greater than j");
    }
    else if (i < j)
    {
        Console.WriteLine("i is less than j");
    }
    else
    {
        Console.WriteLine("i is equal to j");
    }
```
#### ternary operator
* short form of the if else conditions
* If the condition evaluates to true then the first statement will be executed
* otherwise the second statement will be executed

```cs
    condition ? statement 1 : statement 2
```
#### 
* The data type of the variable in the switch and value of a case must be of the same type.
* The value of a case must be a constant or a literal. Variables are not allowed.
* Use `switch` instead of `if-else` if there's a larger number of logical evaluations of a value

```cs
    switch (expression) {
        case value1: 
            // statement
            break;
        case value2: 
            // statement
            break;
        default:
            // default statement
    }
```

OPERATORS
============

### arithmetic operators

x++
x--
int x = 5 % 2; 

TYPES
============

### var

Variables that are declared at method scope can have an implicit "type" `var`.  
An implicitly typed local variable is strongly typed just as if you had declared the type yourself, but the compiler determines the type. 
```cs
    // The following two declarations of i are functionally equivalent:
    var i = 10; // Implicitly typed.
    int i = 10; // Explicitly typed.
```

Arrays
==============

`type[] identifier;` # Sie deklarieren eine Array-Variable, indem Sie an den Datentyp ein Klammernpaar `[]` anfügen


Strings
==============


Formating
--------

### Common

```cs
    string name = "Mark";
    var date = DateTime.Now;
    // Composite formatting:
    Console.WriteLine("Hello, {0}! Today is {1}, it's {2:HH:mm} now.", name, date.DayOfWeek, date);
    // String interpolation:
    Console.WriteLine($"Hello, {name}! Today is {date.DayOfWeek}, it's {date:HH:mm} now.");
    // Both calls produce the same output.
```
* `{index,alignment:formatString}`
* `{interpolationExp,alignment:formatString}`
* *index*|*interpolationExp* # 
  * index: also called a parameter specifier, is a number starting from 0 that identifies a corresponding item in the list of objects
  * interpolationExp: The expression that produces a result to be formatted.
* *alignment* # indicates the preferred formatted field width.
  * if positive: right-aligned --> *alignment* = Abstand nach Links 
  * if negative: left-aligned --> *alignment* = Abstand nach Rechts
* *formatString* # a format string that is appropriate for the type of object being formatted.

### Composite formatting

```cs
    string myname = "Fred";
    String.Format("Name = {0}, hours = {1:hh}", myname, DateTime.Now);
```
```cs
    string[] names = { "Adam", "Bridgette", "Carla", "Daniel","Ebenezer", "Francine", "George" };
    decimal[] hours = { 40, 6.667m, 40.39m, 82, 40.333m, 80,16.75m };
    Console.WriteLine("{0,-20} {1,5}\n", "Name", "Hours");
    for (int ctr = 0; ctr < names.Length; ctr++)
        Console.WriteLine("{0,-20} {1,5:N1}", names[ctr], hours[ctr]);
```

### String interpolation

* `$` # identifies a string literal as an interpolated string. An interpolated string is a string literal that might contain interpolation expressions.



#### Standard numeric format strings

* `C` # Currency. Zeigt die Zahl im lokalen Währungsformat an.
* `D` # Decimal. Zeigt die Zahl als dezimalen Integer an.
* `E` # Exponential. Zeigt die Zahl im wissenschaftlichen Format an (Exponentialschreibweise).
* `F` # Fixed-point. Zeigt die Zahl im Festpunktformat an.
* `P` # Percent. Zeigt die numerische Zahl als Prozentzahl an.
* `X` # Hexadecimal.


CLASSES & OBJECTS
======================

### Constructor

* We can recognize a constructor because its declaration uses the same name as the class and it has no return type.

```cs
    // C# program to illustrate the initialization of an object 
    using System;
    
    // Class Declaration 
    public class Dog { 
    
        // Instance Variables 
        String name; 
        String breed; 
        int age; 
        String color; 
    
        // Constructor Declaration of Class 
        public Dog(String name, String breed, int age, String color) 
        { 
            this.name = name; 
            this.breed = breed; 
            this.age = age; 
            this.color = color; 
        } 
    
        // Property 1 
        public String getName() 
        { 
            return name; 
        } 
    
        // Property 2 
        public String getBreed() 
        { 
            return breed; 
        } 
    
        // Property 3 
        public int getAge() 
        { 
            return age; 
        } 
    
        // Property 4 
        public String getColor() 
        { 
            return color; 
        } 
    
        // Method 1 
        public String toString() 
        { 
            return ("Hi my name is " + this.getName() 
                    + ".\nMy breed, age and color are " + this.getBreed()  
                    + ", " + this.getAge() + ", " + this.getColor()); 
        } 
    
    // Main Method 
    public static void Main(String[] args) 
        { 
            
            // Creating object 
            Dog tuffy = new Dog("tuffy", "papillon", 5, "white"); 
            Console.WriteLine(tuffy.toString()); 
        } 
    } 
```

MODULES
===========


System
---------------------

`using System`  

* `Windows`
  * `Windows.Forms.MessageBox.Show()`  # Meldungsfenster anzeigen
* `Random.Next()`                    # Zufallszahlen erzeugen
* `Convert`                        # Typumwandlungen
  * `Convert.ToDouble()`
  * `Convert.ToString()`
* `Object`
  * `Object.ToString()`                # Ein beliebiges Objekt in eine Zeichenkette konvertieren
* `String`
  * `String.ToLower()`                 # Stringkonvertierung in Kleinbuchstaben
  * `String.ToUpper()`                 # Stringkonvertierung in Großbuchstaben
  * `String.TrimStart()`               # Leerzeichen am Anfang eines Strings entfernen
  * `String.TrimEnd()`                 # Leerzeichen am Ende eines Strings entfernen
* `Array`
  * `Array.GetLowerBound()`            # Array-Untergrenze ermitteln
  * `Array.GetUpperBound()`            # Array-Obergrenze ermitteln
* `String`
  * `ToUpper()`
* `Math`
  * `Math.Sqrt()` 	                    # Returns the square root of a specified number.
  * `Math.Exp()` 	                    # Returns e raised to the specified power.
  * `Math.Pow(double base, double power)`                       # Returns a specified number raised to the specified power.
  * `Math.Max(x, y)` 	                    # Returns the larger of two specified numbers.
  * `Math.Min(x, y)` 	                    # Returns the smaller of two numbers.
  * `Math.Log(double value)`                       # Returns the logarithm of a specified number.
  * 

### console.writeLine

`Console.Write("{0} macht {1}.", strText1, strText2);`

`{N,M: Format}`
* *N* ist ein nullbasierter Zähler um die Variable zu ermitteln.
* *M* gibt die Breite der Ausgabe an.
* *Format* spezifiziert, wie die Daten angezeigt werden

```cs
    double preis = 1.23456789;
    Console.WriteLine("Preis: {0,5:F3}",preis); // Ausgabe: Preis: 1,235 (Breite 5 mit 3 Nachkommastellen, rechtsbündig)
```

Misc
-----

### Comments

/* Comment Comment
Comment Comment */

// comment    