---
title: Network troubleshooting
---

Layer 1
------------

### Duplex Mismatch

In der Praxis ist die häufigste Ursache für Probleme der Duplex-Mismatch im Ethernet. Er tritt auf, wenn die automatisch Aushandlung von Geschwindigkeit und Duplexmode fehlschlägt. Auto-Negotiation funktioniert nur, wenn beide Stationen mitspielen. Ist eine Station fest eingestellt, wird von der anderen nur die Geschwindigkeit erkannt und Halfduplex gefahren. 

Gerät A     |Gerät B        |Resultat
------------|---------------|-----------
100 half 	|100 half 	    |Halfduplex, bei Last Kollisionen
100 half 	|100 full 	    |Duplex-Mismatch , viele Fehler, keine Performance
100 half 	|auto 	        |Glück gehabt, B geht auf 100 half
100 full 	|100 full 	    |immer noch die sicherste Methode
100 full 	|auto 	        |Duplex-Mismatch, B geht auf 100 half, viele Fehler, keine Performance
auto 	    |auto 	        |mit etwas Glück gehen A und B auf 100 full
100 	    |10 	        |Speed-Mismatch, kein Link

### Doppelte IP-Adressen

Doppelte IP-Adressen sollte es eigentlich nicht geben. Jeder Host hat vor der Aktivierung seiner Adresse einen Test auf Eindeutigkeit durchzuführen. Dazu sendet der Host mehrere ARP-Requests an sich selbst. Auf diese ARP-Request sollte also keiner antworten.

Im Zeitalter der mobilen Endgeräte funktioniert das leider nicht immer so. Und es gibt auch noch genügend IP-Stacks die sich nicht um irgendwelche RFC's kümmern.

Mit doppelten IP-Adressen ergibt sich ein Fehlerbild ähnlich dem bei doppelten MAC-Adressen. Je nach dem welcher Host gerade im ARP-Cache des Default Routers steht, funktioniert die Kommunikation oder eben nicht. Im Eventlog von Windows, wird die Erkennung doppelter IP-Adressen protokolliert.

### Doppelte Hostnamen

Nicht eindeutige Hostnamen sind besonders im Windowsumfeld problematisch. Dort registrieren sich die Maschinen mit ihren Hostnamen beim WINS-Server. Problematischer ist allerdings das Computerkonto der betroffenen Maschinen auf dem Domaincontroller. Der Betreiber der zweiten Maschine wird für seinen PC ein Computerkonto erstellen. Dadurch wird das Konto der anderen Maschine überschrieben. Beim Booten für das zu einer Meldung „Das Vertrauensverhältnis zur primären Domäne ist gestört...“ und es ist keine Anmeldung am Domaincontroller möglich. 