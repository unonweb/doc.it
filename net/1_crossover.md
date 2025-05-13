---
title: Crossover Connection
---

#### Auto-MDIX
* automatic medium-dependent interface crossover.
* detects whether you need a crossover cable or a straight-through cable, and it automatically configures the network interface card accordingly.

Neuere 100-Mbit/s-Switches und -Netzwerkkarten sowie praktisch alle Gigabit-Geräte (1000BASE-T) beherrschen die Fähigkeit, selbstständig die Sende- und Empfangsleitungen des angeschlossenen Geräts zu erkennen und sich darauf einzustellen. Dies bezeichnet man als Auto MDI-X. Hierbei ist die Verwendung des Kabeltyps (gekreuzt oder ungekreuzt) egal.

Gigabit and faster Ethernet links over twisted pair cable use all four cable pairs for simultaneous transmission in both directions. For this reason, there are no dedicated transmit and receive pairs, and consequently, crossover cables are never required for 1000BASE-T communication.

To connect two ethernet LAN card interfaces together, at least one of them must support Auto-MDIX, or you will need a cross-over ethernet cable. This is a special cable which connects the "TRANSMIT" lines of one LAN card interface to the "RECEIVE" lines of the other and vice versa. However if at least one of the two cards supports Auto-MDIX, then the cross-over function will be taken care of automatically.