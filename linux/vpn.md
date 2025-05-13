### tun/tap device

tun/tap interfaces are software-only interfaces, meaning that they exist only in the kernel 

Während sich normalerweise hinter einem Netzwerkgerät (z.B. eth0) direkt eine entsprechende Hardware in Form einer Netzwerkkarte verbirgt, werden Pakete, die an ein TUN/TAP-Gerät gesendet werden, an ein Programm im Userspace weitergeleitet und andersherum.

- can be **transient**, meaning that it's created, used and destroyed by the same program; when the program terminates, even if it doesn't explicitly destroy the interface, the interfaces ceases to exist.
- can be **persistent**; in this case, it is created using a dedicated utility (like tunctl or openvpn --mktun), and then normal programs can attach to it
- **tun** simuliert ein Ende-zu-Ende-Netzwerkgerät (Layer 3) und transportiert IP-Pakete
- **tan** simuliert eine Punkt-zu-Punkt-Verbindung via Ethernet-Gerät (Layer 2); transportiert Ethernet-Frames