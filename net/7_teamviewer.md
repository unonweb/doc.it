
* Da der gesamte Datenverkehr über **Port 80** läuft, müssen Sie keine Einstellungen an Firewall oder Router vornehmen.
* **encryption**: AES 256-bit

#### Verbindung trotz NAT & dynamischer IP?  

Teamviewer hält eine permanente Verbindung mit einem Vermittlungs-Server des Herstellers aufrecht. Dieser Server kann dadurch jederzeit Daten durch die NAT-Funktion und die Firewall des Routers hindurch an den PC senden, auf dem das Tool installiert ist. Kommt es zu einer Verbindungstrennung beziehungsweise Zwangstrennung, baut Teamviewer die Verbindung schnellstmöglich wieder auf. Daher kennt der Vermittlungs-Server auch immer die aktuelle öffentliche IP-Adresse des Routers.

Statt einer IP-Adresse benutzen Sie zum Verbinden von einem anderen Gerät aus die Teamviewer-ID des fernzusteuernden Rechners. Diese Nummer wird beim allerersten Verbindungsaufbau mit dem Teamviewer-Server nach der Installation festgelegt und ändert sich nicht. 

Vereinfacht gesagt veranlasst dieser dann über die bestehende Verbindung B dazu, eine weitere Verbindung nach außen zu öffnen. In diese klinkt sich A dann ein. Da dieser Vorgang die ursprünglichen Spezifikationen der NAT-Funktion über die Grenzen hinaus ausreizt, klappt das nicht mit jedem Router. Sollte die Verbindung fehlschlagen, übernimmt der Vermittlungs-Server den Transport der Daten von A nach B und umgekehrt. 

* `B <> TV-Vermittlungs-Server` (stehende Verbindung)

A soll per Teamviewer auf den PC zuhause (B) zugreifen:

1) `A >> TV-Vermittlungs-Server`
2) `TV-Vermittlungs-Server >> B`
3) `B >> A`

### Ports

#### Port 5938/tcp/udp

TeamViewer stellt ausgehende TCP- und UDP-Verbindungen bevorzugt über Port 5938 her. Dies ist der Port, der hauptsächlich genutzt wird und über den TeamViewer am besten arbeitet. Ihre Firewall sollte mindestens dies zulassen.

#### Port 443/tcp

Falls TeamViewer die Verbindung nicht über Port 5938 herstellen kann, wird als nächstes der Verbindungsaufbau über TCP Port 443 versucht.
Unsere mobilen Apps für Android, iOS, Windows Mobile und BlackBerry verwenden Port 443 jedoch nicht.

Hinweis: Port 443 wird auch von unseren personalisierten Modulen verwendet, die in der Management Console erstellt werden. Wenn Sie ein personalisiertes Modul verwenden, beispielsweise durch Group Policy, muss Port 443 auf den von Ihnen eingesetzten Computern geöffnet sein. Port 443 wird auch noch für einige andere Dinge verwendet, wie die Überprüfung der TeamViewer Updates.

#### Port 80/tcp

Falls TeamViewer keine Verbindung über Port 5938 oder Port 443 aufbauen kann, wird der Verbindungsaufbau über TCP Port 80 versucht. Die Verbindungsgeschwindigkeit ist über diesen Port niedriger und weniger zuverlässig als über Port 5938 oder Port 443. Dies liegt an den zusätzlichen Verwaltungsdaten, die er verwendet. Außerdem gibt es keine automatische Wiederherstellung der Verbindung, wenn diese vorübergehend unterbrochen wird. Aus diesen Gründen wird Port 80 nur als letztes Mittel eingesetzt.
Unsere mobilen Apps für Android, Windows Mobile und BlackBerry verwenden Port 80 nicht. Allerdings können unsere iOS Apps Port 80 bei Bedarf nutzen.