---
title: vpn vs proxy
---

* Although they are fundamentally different, VPNs and proxies have a single thing in common: they both allow you to appear as if you are connecting to the internet from another location. 

# Proxy

* proxy server connections are configured on an application-by-application basis, not computer-wide. You don’t configure your entire computer to connect to the proxy–you configure your web browser, your BitTorrent client, or other proxy-compatible application. 
* The two most common proxy server protocols are HTTP and SOCKS.
* SOCKS is indifferent to the type of traffic that passes through it.
* Where HTTP proxies can only handle web traffic, a SOCKS server will simply pass along any traffic it gets, whether that traffic is for a web server, an FTP server, or BitTorrent client

# VPN

* VPNs *are set up at the operating system level*, and the VPN connection captures the entire network connection of the device it is configured on. This means that unlike a proxy server, which simply acts as a man-in-the-middle server for a single application (like your web browser or BitTorrent client), VPNs will capture the traffic of every single application on your computer, from your web browser to your online games to even Windows Update running in the background.
* Furthermore, this entire process is all passed through a heavily encrypted tunnel between your computer and the remote network. This makes a VPN connection the most ideal solution for any sort of high-stakes network use where privacy or security is a concern. With a VPN, neither your ISP nor any other snooping parties can access the transmission between your computer and the VPN server. 

### split tunnel

Es werden nur diejenigen Verbindungen durch den VPN-Tunnel geleitet, welche Systeme am anderen Ende des VPN-Tunnels als Ziele haben. Für alle anderen Verbindungen wird der VPN-Tunnel ignoriert. 

* Internet --> lokales Gateway
* Firmennetz --> VPN-Gateway

Die technische Umsetzung von Split Tunneling erfolgt mittels Änderungen in der *Routingtabelle* des Hosts. Für jedes erreichbare IP-Netzwerk am anderen Ende des VPN-Tunnels wird ein Eintrag in der Routingtabelle des Rechners gesetzt. Dabei dient die IP-Adresse des entfernten Endpunktes des VPN-Tunnels als *Next Hop*. 

* split-include: A split tunnel configured to only tunnel traffic destined to a specific set of destinations.
* split-exclude: When configured to accept all traffic except traffic destined to a specific set of destinations.


#### Vorteile Split-Tunnel

* Zugriff auf Ressourcen auf beiden Seiten
* Reduktion des Datenvolumens der Firma

#### Nachteile Split-Tunnel

* Sichheitsmaßnahmen der Firma können umgangen werden (Firewall)