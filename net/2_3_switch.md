# Switch

* **Hub** [Multiport-Repeater]: gibt alle Pakete immer auf allen Ports aus

* **Switch** [Multiport-Bridge]: Funktion eines Hubs + MAC-Adressen zu lesen und zu speichern

  In geswitchten Netzwerken gelangt mit Ausnahme von Broadcasts immer nur derjenige Traffic an eine bestimmte Schnittstelle, der auch für diese bestimmt ist; d.h. ein Switch schaltet intern immer nur die gerade erforderlichen Routen, was Kollisions­domänen im Unterschied zum Hub auf den jeweiligen Port beschränkt.

* **Switches brechen die Ethernet-Busstruktur in eine Bus-/Sternstruktur auf**. Teilsegmente mit Busstruktur werden sternförmig über je einen Port des Switch gekoppelt.

* **Layer 2 Switch**: speichert temporär MAC-Adressen und ordnet sie den Ports zu; Antworten können somit gezielt auf dem passenden Port ausgegeben werden; IP-Adressen werden nicht gespeichert/zugeordnet

* **Layer 3 Switch**: speichert IP- und MAC-Adressen; führt ARP-Tabellen; 

### Auslastung

* Die Datenlast sollte nach Möglichkeit gleichmäßig über die Ports verteilt werden. Systeme, die viele Daten übertragen, müssen unter Umständen an einen eigenen Switch-Port angeschlossen werden (Private Ethernet)
* Systeme die viel miteinander kommunizieren, an einen gemeinsamen Port anschließen, um so die Datenmengen, die mehr als ein Segment durchlaufen müssen, zu reduzieren. 

Wenn ein Switch ein Datenpaket empfängt, liest er die MAC-Adresse des Senders aus und verknüpft diese in der MAC-Adress-Table mit dem Port auf dem er das Paket empfangen hat: `Port 1: <MAC-Adresse>`. Kommt die Antwortet, weiß der Switch schon hinter welchem Port der Empfänger wartet und muss somit nicht mehr auf allen Ports senden.

* Kollisionen innerhalb des Switches werden durch dedizierte Leitungen vermieden. Dadurch entfällt die Notwendigkeit der Netzwerkkarte auf einem der Drähte nach Kollisionen zu lauschen. Es kann somit auf **Full Duplex** geschaltet werden.

### Sicherheitsgewinn

* minimiert Broadcast anfragen; es kann weniger Netzwerkverkehr ausgelesen werden (dies kann umgangen werden, indem ein Monitor-Port genutzt wird, auf dem bei vielen Switches alle Pakete zusätzlich nochmal ausgegeben werden)
* ein Layer 3 Switch kann über einen MAC-Filter auch die Sicherheit erhöhen; kann i.d.R. administriert werden

## vlan

Für das VLAN-Tag wurde der Ethernet-Frame erweitert (es wurde nachträglich hinzugefügt). Ältere Switches verwerfen diese Pakete, da sie für den Ethernet-Standard zu groß sind!

### Troubleshooting

* Port-Statistiken
* Ältere Switches verwerfen Ethernet-Frames mit VLAN-Tag, da sie für den Ethernet-Standard zu groß sind!