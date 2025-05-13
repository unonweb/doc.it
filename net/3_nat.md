
# SNAT (Source-NAT)

Am meisten findet Source-NAT Anwendung aufgrund der Knappheit öffentlicher IPv4-Adressen und der Tendenz, immer mehr Haushalte (in denen private Subnetze verwendet werden) mit dem Internet zu verbinden. Die spezielle Form der SNAT wird auch *Masquerading* bzw. Masquerade genannt, was vor allem bei Einwahlverbindungen genutzt wird. Beim Maskieren wird automatisch durch einen Algorithmus die Absender-Adresse des Pakets in die IP-Adresse des Interfaces geändert, auf dem das Paket den Router verlässt, während beim SNAT die (neue) Quell-Adresse explizit angegeben werden muss.

Gerade in privaten oder möglichst preisgünstig ausgeführten Netzinstallationen wird Source-NAT als eine Art Sicherheitsmerkmal und zur Trennung von internen und externen Netzen eingesetzt. Durch das Maskieren der Quell-IP-Adresse können tatsächlich die internen Rechner nicht mehr von außen direkt angesprochen werden, was aber eher als Nebeneffekt zu betrachten ist, da es weder Sicherheitsinfrastruktur ersetzt noch zur Trennung von Netzen vorgesehen ist.

# DNAT (Destination-NAT)

* wird verwendet, um das Ziel eines IP-Pakets zu ändern. 

Am häufigsten findet DNAT Verwendung beim Ändern der öffentlichen IP eines Internet-Anschlusses in die private IP-Adresse eines Servers im privaten Subnetz. Diese Methode ist als „Port-Forwarding“ in Verbindung mit UDP / TCP - Verbindungen bekannt. DNAT kann daher auch dazu genutzt werden, um mehrere, unterschiedliche Serverdienste, die auf verschiedenen Computern betrieben werden, unter einer einzigen (öffentlichen) IP-Adresse anzubieten. Siehe zur Abgrenzung von DNAT auch NAT-Traversal bzw. NAT-T. 

# NAT-T (traversal)

- https://en.wikipedia.org/wiki/Hole_punching_(networking)
- Establish a direct connection between two parties in which one or both are behind firewalls or behind routers that use NAT
- To punch a hole, each client connects to an unrestricted third-party server that temporarily stores external and internal address and port information for each client

## Hole punching

- https://en.wikipedia.org/wiki/Hole_punching_(networking)
- involves a third party service relay server
- A und B stellen aktiv die Verbindung zum Relay-Server her, sodass keine Portweiterleitung erforderlich ist.
- Both routers/firewalls see outgoing packets thus allowing the "returning" packets

The technique used by teamviewer (and maybe other apps) is called "hole punching". It involves a third party service (in this case the teamviewer server) which initially is contacted by the clients. Later these clients get told about the other clients ip address. They both now start to contact the other clients ip via prearranged ports so that both firewalls (nat-routers) see outgoing connections to the other client. Example:

- Client 1 (123.123.123.123:9999) sends packets to Client 2 (124.124.124.124:8888) while
- Client 2 (124.124.124.124:8888) sends packets to Client 1 (123.123.123.123:9999)

Both routers/firewalls see outgoing packets thus allowing the "returning" packets. **That only works because of the fact that UDP is connectionless**.

Relay-Server (wenn der Client kein NAT-Punching durchführen kann, verwendet er diesen Server, um die Verbindung weiterzuleiten)