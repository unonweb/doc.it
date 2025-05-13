IP
========

### Primäre Aufgabe

* Ist das Ziel lokal oder remote?  
  --> Netzwerk-ID berechnenen

#### Zur Verfügung stehende Informationen

* eigene IP-Adresse
* eigene Subnetzmaske
* remote IP-Adresse
* remote Subnetzmaske

### Berechnung der Netzwerk-ID

<pre>
* lokal
  * IP-Adresse:       192.168.1.100
  * Subnetzmaske:     255.255.255.0
  --> Netzwerk-ID:    192.168.1.0
* remote
  * IP-Adresse:       192.168.2.10
  * Subnetzmaske:     255.255.255.0
  --> Netzwerk-ID:    192.168.2.0
</pre>


--> Wo die Subnetzmaske den Wert **255** hat, bleibt die IP-Adresse, wie sie ist.  
--> Wo die Subnetzmaske den Wert **0** hat, wird die IP-Adresse auch 0.

# IP-Adressen

#### Binär

`XXXXXXXX.XXXXXXXX.XXXXXXXX.XXXXXXXX.`

#### Dezimal

`0-255.0-255.0-255.0-255.`

* Vier Zahlenblöcke
* Zahlenblock = Oktett = 1 Byte
* 11111111 = 255

## CIDR

**Classless Inter-Domain Routing**

* Das Suffix gibt die Anzahl der 1-Bits in der Netzmaske an.

`/8`  --> `255.0.0.0`  
`/16` --> `255.255.0.0`  
`/24` --> `255.255.255.0`  
`/25` --> `255.255.255.128` 

## Klassen & Private Adressen

* Unterscheidung anhand des ersten Bytes

### Klasse A

* Adressen: `1-126.X.Y.Z`
* Subnetz: `255.0.0.0`
* Netzwerk/Host: `N.H.H.H`
* Privat: `10.0.0.0` - `10.255.255.255`
* Anzahl Netze: 126
* Anzahl Hosts: 16 Millionen
* Besondere Adressen: 
  * `127.0.0.1` # Loopback

### Klasse B

* Adressen: `128-191.X.Y.Z`
* Subnetz: `255.255.0.0`
* Netzwerk/Host: `N.N.H.H`
* Privat: `172.16.0.0` - `172.31.255.255`
* Anzahl Netze: 16 Tausend
* Anzahl Hosts: 65 Tausend

### Klasse C

* Adressen: `192-223.X.Y.Z`
* Subnetz: `255.255.255.0`
* Netzwerk/Host: `N.N.N.H`
* Privat: `192.168.0.0` - `192.168.255.255` bzw. 192.168/16
* Anzahl Netze: 2 Millionen
* Anzahl Hosts: 254 Hosts

### Klasse D

* Adressen: `224-239.X.Y.Z`
* Subnetz: `255.255.255.255`
* Netzwerk/Host: `N.N.N.N`
* Anzahl Netze: 15
* Anzahl Hosts: -
* Verwendung für **Multicastadressen**

### Klasse E

* Adressen: `240-247.X.Y.Z`
* Subnetz: `255.255.255.255`
* Netzwerk/Host: `N.N.N.N`
* Anzahl Netze: 7
* Anzahl Hosts: -

### Private Adressen

* **10.0.0.0/8** (`10.0.0.0` bis `10.255.255.255`):  
* **172.16.0.0/12** (`172.16.0.0` bis `172.31.255.255`):  
* **192.168.0.0/16** (`192.168.0.0` bis `192.168.255.255`):  
* **169.254.0.0/16** (`169.254.0.0` bis `169.254.255.255`):  
  Link-local Adresses für IPv4LL.

## Fragen & Berechnungen

#### Wie viele Rechner gehören maximal zu einem Netz?

--> kann mit Hilfe der Subnet-Mask beantwortet werden.

Ein Netzwerk hat die Subnet-Mask 255.255.255.0. 
Für die Host-ID verbleiben 8 Bit. 
Somit gibt es zunächst 2^8 = 256 Möglichkeiten für die Host-ID. 
Die Bits der Host-ID dürfen weder alle 1 noch alle 0 sein. Somit verbleiben 254 Möglichkeiten für die Host-ID. 

#### CIDR --> Decimal

`10.0.0.0/20`

* die ersten 20 Bits stehen auf 1
* somit stehen die ersten 2 Byte auf 1: 255.255.x.x
* somit bleiben im dritten Byte 4 1er Bits auf den Positionen 2⁷+2⁶+2⁵+2⁴ (= 240)
* somit ergibt sich: `255.255.240.0`