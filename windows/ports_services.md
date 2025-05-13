
`%SystemRoot%\system32\drivers\etc\services`

* **`88`**          # **kerberos**
* **`135`**         # **RPC-EPMAP**: endpoint mapper für Remote Procedure Calls
  * DCE Endpunkt-Auflösung (Distributed Computing Environment)
  * You can bind to that port on a remote computer, anonymously, and either enumerate all the services (endpoints) available on that computer, or you can request what port a specific service is running on if you know what you're looking for.
* **`137-139`**     # **NetBIOS over TCP/IP**
  * `137`/udp/tcp   # NetBIOS-Namensauflösung (**WINS**)
  * `138`/udp       # Datagram Service
  * `139`/tcp       # Session Service
  * früher wurde SMB in *NetBIOS over TCP* gekapselt
* **`389`**         # **LDAP**
* **`636`**         # **LDAP-SSL**
* **`445`**         # **SMB** over IP  
  * die gesamte SMB-Kommunikation (Common Internet File System)
  * wird von den Microsoft *Directory Services* genutzt
  * WannaCry-Infektion erfolgte überwiegend über den TCP-Port 445
  * gemeinsamen Zugriff auf Drucker
  * Microsoft-DS File Sharing
* **`3268`**/udp   # LDAP GC
* **`3269`**/tcp   # LDAP GC SSL
* **`3389`**: **RDP** (TCP/UDP)
* **`5985`**: **WinRM** Microsoft Windows Remote Management

### Linux

* **`111`**/tcp     # a port mapper with similar functions to Microsoft's port 135
* **`111`**/udp