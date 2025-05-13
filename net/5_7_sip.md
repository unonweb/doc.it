
# LINKS

- https://www.3cx.de/voip-sip/pbx/

# SIP

- https://en.wikipedia.org/wiki/Session_Initiation_Protocol

**Session Initiation Protocol** is a signaling protocol used for initiating, maintaining, and terminating communication sessions that include voice, video and messaging applications.

- VOIP
- mobile phone calling over LTE (VoLTE)

SIP is a text-based protocol, incorporating many elements of HTTP and the SMTP.

SIP is designed to be independent of the underlying transport layer protocol and can be used with UDP, TCP and SCTP (Stream Control Transmission Protocol).

- may be encrypted with TLS

## Network elements

The network elements that use the Session Initiation Protocol for communication are called **SIP user agents**. Each user agent (UA) performs the function of a **user agent client** (UAC) when it is requesting a service function, and that of a **user agent server** (UAS) when responding to a request. Thus, **any two SIP endpoints may in principle operate without any intervening SIP infrastructure**. However, for network operational reasons, for provisioning public services to users, and for directory services, SIP defines several specific types of network server elements. Each of these service elements also communicates within the client-server model implemented in user agent clients and servers.

# PBX

- **Private Branch Exchange**
- engl Bezeichnung für eine Telefonanlage
- Privates Netzwerk aus Telefonen, das als eigenes System an das allgemeine Telefonnetz, auch **PSTN** (**Public Switched Telephone Network**) bezeichnet, angebunden ist.
- Risiko: **PBX Hacking**

### Funktionen

herkömmlich:
- Durchwahlen
- Warteschleifen
- kostenlose Anrufe innerhalb der Firma durch Verknüpfung von Nebenstellen

IP-PBX:
- Videokonferenzen mit WebRTC
- IVR/Sprachmenüs
- Warteschleifen
- Fax-zu-E-Mail
- Call Flow Designer

# SIP-Trunk

 - IP-Datenverbindung über das Internet in das lokale Netz (Amtsverbindung)
 - Diese Verbindung muss eine oder mehrere Firewalls passieren
 - Verbindung
     - Internet - PBX
     - PBX - Telefon intern
 - SIP-Trunk Provider
   z.B. TKS Reutlingen
 - 

# SBC

- **session** in the context of telephony, this would be a **call**
- **border** the edge of a corporate network
- **wherever you have a firewall you need to add a SBC**
- Aufgaben:
    - Steuerung und Kontrolle über Signalisierung und optional Media-Datenströme (Voice, Video)
    - allow only authorized calls to enter through the company's border
    - controlls VOIP-Traffic while applying real-time security and policies
    - Trennung internes/externes Netz
    - Terminierung der Signalisierung: 
      TRUNK (extern, unsicher) / PBX (intern, sicher)
    - Aushandlung von Verschlüsselung (TLS/SRTP)
    - Protokollübersetzung
      (z.B SIP / H.323 / SIP-Q)
    - Header-Manipulation
      (z.B Rufnummeranpassung)

## franz.K

- SBC wird gebraucht wenn sich der 3CX-Server und die Telefone in unterschiedlichen Netzwerken befinden.
- Wird nur im Fall der Hardphones gebraucht.
- Baut eine getunnelte Verbindung zwischen dem internen Netz und dem TKS-Netz auf, wo der 3CX-Server steht 
  vgl. VPN: SBC = Client; 3CX = Server
- Daher müssen keine Ausnahmen in der Firewall geregelt werden
- **Über diese Verbindung findet die externe Telefonanlage dann die internen Telefone**
- Weiterer Vorteil: Mit einem SBC und der Verwendung einer externen PBX können interne Gespräche intern geroutet werden