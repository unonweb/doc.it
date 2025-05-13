
# VPN vs. RDP

## VPN

* *use local device and remote IP address*
* *connection between a computer and a remote network* (allowing access to remote resources as if you were inside that same physical remote network)
* allows you to *change your IP address*

### advantages

* Die Verarbeitung von VPN-Anmeldeinformationen ist oft einfacher
* Der Schaden, wenn sich jemand schlichtweg im Unternehmensnetzwerk befindet (und nicht direkt in einem Unternehmenscomputer), ist nicht so groß.

### disadvantages / security considerations

* While using a VPN service, your device connects to a **VPN server**. You cannot directly control the VPN server, which is the case with a Remote Desktop. 


## RDP

* *use remote device and remote IP address*


# Hybrid: VPN & Remote Desktop

## TeamViewer >> RDP

TV-Verbindung auf einen ausgewählten "Gateway"-PC, von dem aus die lokale IT eine weitere RDP-Verbindung herstellt.

## VPN >> TeamViewer (VPN Gateway)

Eine VPN Verbindung bewirkt, dass sich Ihr Client im lokalen Netz der Firma anmelden kann, ohne wirklich vor Ort zu sein. Alleine durch die Einrichtung einer VPN Verbindung kann der nach außen offene RDP Port bereits geschlossen und die internen Systeme besser geschützt werden.

* Ein VPN-Gateway installieren, um alle RDP-Verbindungen zu vermitteln, die von außerhalb des lokalen Netzwerkes aufgebaut werden.
* In Betracht ziehen, geoIP-Sperren am VPN-Gateway einzurichten.