RPC
============

**remote procedure call**

* Technik zur Realisierung von Interprozesskommunikation

Der Trick bei RPC beruht darauf, dass alle Dienste einen zentralen Port als Auskunft ansprechen und den gewünschten Service anfordern. Dieser **Endpointmapper** teilt dem aufrufenden Prozess dann die tatsächliche Adresse der gewünschten Funktion mit, sofern diese vorhanden ist. Die SUN-Version nutzt einen RCP Portmapper auf Port 111 während Microsoft den vielfach bekannten Port **135** hierfür nutzt.

### Ablauf

Jede Funktion, die daher RPC nutzt, muss folgende Schritt durchlaufen:

1. **Verbindungsaufbau mit TCP Handshake**  
   Über drei Pakete wird eine TCP/IP-Verbindung zum Portmapper (Port 135) aufgebaut
2. **RPC-Anforderung**  
   Mit dem vierten Paket fordert der Client unter Angabe einer eindeutigen Kennung (uuID) eine Bindung an
3. **RPC Antwort**  
   Der Portmapper antwortet mit einer ACK-Bestätigung. Die Verbindung steht. Gibt es den angeforderten Dienst nicht, dann erfolgt eben eine NACK-Meldung. Das Antwortpaket kann auch Informationen darüber enthalten, ob die gewünschte Funktion auch auf einem anderen Port verfügbar ist.
4. **RPC Anfrage und Antwort**  
   Nun kann der Client mit dem entsprechenden Serverprozess Daten austauschen. Die Kommunikation kann weiterhin über den Portmapper aber auch über andere Kommunikationswege erfolgen.
5. **Verbindungsabbau**  
   Drei weitere TCP-Pakete beschließen die Verbindung geordnet.