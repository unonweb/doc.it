# Windows

## mechanism

1) Local Host Name
2) Datei *Hosts*  
   --> `C:\Windows\System32\drivers\etc\hosts`
3) DNS-Server
4) NetBIOS-Namenscache
5) WINS
6) Broadcast
7) Datei *LMHosts* (nur NetBios-Computer)

# Linux

## mechanism

1. eine Domain wird angefragt (firefox|ping|dig|etc)
2. die Namensanfrage wird an das OS weitergegeben
3. das OS befragt `/etc/nsswitch.conf` in welcher Reihenfolge vorgegangen werden soll
4. ggf. schaut das OS zuerst in `/etc/hosts` und `/etc/hostname` nach
5. falls kein passender Eintrag vorliegt, wird die Anfrage einem DNS-Client übergeben
6. der DNS-Client durchsucht seinen Cache und - falls der Eintrag nicht dabei ist - kontaktiert einen DNS-Server (vom ISP vorgegeben und durch DHCP automatisch bezogen) oder explizit angegeben über `/etc/resolv.conf` bzw. `/etc/netplan/01-netcfg.yaml` (falls netplan verwendet wird)
7. der DNS-Server übergibt die Antwort/zugehörige IP-Adresse an den DNS-Client

## local name resolution

### /etc/hosts

```sh
sudo xed * /etc/hosts # lokale Namensauflösung; historisch ein Vorläufer von DNS; wird heute insbesondere für die Zuordnung von localhost und loopback verwendet; es können hier aber auch Host eingetragen werden, die DNS nicht auflösen kann oder Aliase für einen FQDN gesetzt werden; ein falscher Eintrag hier kann den Zugriff auf bestimmt Adressen verhindern; existiert auf fast allen OS.
```

```
IPv4/6		FQDN			          Alias
127.1.1.1	frida.example.com	  [alias1] [alias2]
```
```ini
127.0.0.1   manueldieterich.local
127.0.0.1   radjajuschka.local
127.0.0.1   haerer-geruestbau.local
127.0.0.1		alpaca-arts.local
127.0.0.1		custom-elements.local
127.0.0.1		sites.local
```



* `/etc/networks` # lokale Namensauflösung für Netzwerkadressen

### hostname & nsswitch

Für die Auflösung des eigenen Host-Namens muss derselbe Eintrag unter `/etc/hosts` und `/etc/hostname` hinterlegt sein.

```sh
sudo xed /etc/nsswitch.conf  # konfiguriert libc.so
# hosts: files dns  # schaue zuerst in den (beiden) lokalen Dateien für Namensauflösung nach
sudo xed /etc/hostname       # eigener/statischer Host-Name; wird beim Boot eingelesen und gesetzt; wird z.B. für den Shell-Prompt verwendet; kann von `hostnamectl` geändert werden
hostnamectl
cat /proc/sys/kernel/hostname
```

DNS Client / Resolver
=======================

Schnittstelle zwischen Anwendung und Nameserver; Aufgabe: Informationen von Nameservern abrufen; Ein Resolver arbeitet entweder *rekursiv* oder *iterativ*.

a) *rekursiv*: der Resolver verschickt eine rekursive Anfrage an den NS; falls dieser die gewünschte Information nicht hat, delegiert er die Anfrage; somit überlässt der Resolver die Arbeit zur vollständigen Auflösung dem Nameserver; die meisten Resolver von Clients arbeiten ausschließlich rekursiv, sie werden dann auch als *Stub-Resolver* bezeichnet.
b) *iterativ*: der Resolver bekommt entweder den gewünschten Resource Record oder einen Verweis auf weitere Nameserver, die er als Nächstes fragt; so hangelt sich der Resolver von NS zu NS, bis er eine verbindliche Antwort erhält; Nameserver besitzen i.d.R. eigene Resolver. Diese arbeiten gewöhnlich iterativ.

systemd-resolve
-------------------

* Standard unter Ubuntu 18.04
* verknüpft sich mit der IP-Adresse *127.0.0.53*. Das ist die Adresse des lokalen Caches.

```sh
systemd-resolve --status # shows the global and per-link DNS settings in currently in effect
systemd-resolve --statistics # shows general resolver statistics, including information whether DNSSEC is enabled and available, as well as resolution and validation statistics
systemd-resolve --set-dns ip 
```

zones
=============

#### Forward Lookup Zone

Name --> IP-Adresse  
(Telefonbuch)

#### Reverse Lookup Zone

IP-Adresse --> Name  
(für Kontrolle und Tests)

### Zonentyp

#### Primäre Zone

* Speichert Zuordnungen in einer Textdatei lokal auf dem Server

#### Sekundäre Zone

* Zweck: Lastenverteilung, Fehlertoleranz
* Keine Änderung der Daten möglich (Schreibschutz)

# encryption

Just putting `1.1.1.1` in your DNS network settings does not provide any security at all. Communications between your browser and the Cloudflare server is unencrypted and still visible to your ISP.

## DNS over TLS (DoT) (2016) 

* DNS over HTTPS (DoH) (2018)

Grundlage ist in beiden Fällen TLS, mit der auch die Kommunikation zwischen Ihnen und einer Website durch HTTPS abgesichert wird. 

## DNS over TLS

Bei DNS over TLS wird die ursprüngliche DNS-Nachricht direkt in den sicheren TLS-Kanal eingebettet. Von außen kann man den abgefragten Namen weder feststellen noch ändern. 

Ein Problem bei der Einführung eines neuen Ports besteht darin, dass  vorhandene Firewalls ihn möglicherweise blockieren. Entweder, weil sie  mit einer Whitelist arbeiten, bei der neue Dienste ausdrücklich  aktiviert werden müssen, oder weil sie mit einer Sperrliste arbeiten und ein Netzwerkadministrator einen Dienst ausdrücklich blockiert. Wenn die Verfügbarkeit bei der sicheren Option (DoT) nicht so gut ist wie bei  der unsicheren Option, könnten Nutzer und Anwendungen versucht sein,  wieder zu unverschlüsseltem DNS zurückzukehren. Angreifer könnten  dadurch die Möglichkeit haben, Benutzer zu einer unsicheren Version zu zwingen.

Da die Kommunkation mit dem verschlüsselten DNS-Server über den **Port 853** läuft, muss dieser geöffnet sein und darf nicht vom Provider oder  Internet-Anbieter blockiert werden

## DNS over HTTPS

DoH ist im Wesentlichen HTTPS, der gleiche verschlüsselte Standard, der auch im Web verwendet wird, und benutzt die gleiche Portnummer (tcp/443).

wurde vor allem für zwei Anwendungsfälle entwickelt:

* Das Problem der Portblockierung sollte beseitigt werden
* Webanwendungen sollten über vorhandene Browser-APIs auf DNS zugreifen können.

# subdomains

* können mit CNAME einträgen verwaltet werden

Beispiel

# record types

```
<name> <ttl> <class> <type> <priority> <rdata>
```

* **`<name>`** wird bei allen DNS Einträgen als Fully Qualified Domain Names (FQDN) eingetragen. Er bildet den kompletten Pfad, von der kleinsten Einheit (Subdomain) ganz links bis zur größten Einheit (Root) ganz rechts, ab.
* **`<ttl>`**: Time to live; legt fest, wie lange die abgefragten Informationen gültig sind. Der Client speichert die abgefragten Informationen also nur für die Dauer dieses Zeitraums in seinem Cache. Auf diese Weise wird garantiert, dass keinerlei veraltete Einträge verwendet werden. Die Angabe zur Time to live erfolgt dabei in Sekunden.
* **`<class>`** bezieht sich auf die Netzwerkart und ist mittlerweile optional, da sich alle Anfragen auf das Internet beziehen. Das Feld ist ein Überbleibsel aus den Anfängen der Netzwerkkommunikation, als es noch weitere Netzwerkklassen gab. In DNS Einträgen ist das Feld daher entweder leer oder enthält das Kürzel **IN** (Internet).
  * `IN`
* `<type>` legt die Art des DNS Eintrags fest
  * `MX`
* **`<priority>`** ist speziell für MX Records vorgesehen. Es legt die Priorität des Mailservers fest. Dabei gilt: Je kleiner der Wert, desto höher die Priorität. Hintergrund ist die Tatsache, dass auf vielen größeren Webprojekten oftmals mehrere Mailserver parallel laufen, um Ausfälle durch Belastungsspitzen etc. auszugleichen. Anfragen werden hier also zuerst an den Server mit dem geringsten Wert gesendet, ist dieser nicht verfügbar, werden sie an den Server mit dem nächsthöheren Wert weitergeleitet.
* **`<rdata>`** enthalten schließlich den Namen des Mailservers, der in Form des FQDN angegeben wird

```
vautron.de.              IN              A                            	 xxx.xxx.xxx.xxx (IP Adresse des Servers)
vautron.de.              IN              MX              10              mail1.vautron.de.
vautron.de.              IN              MX              20              mail2.vautron.de.
```

## a-record

- Apex, or root record
- maps a *hostname* to an IPv4 address of a server

## cname

* Canonical name
* used to specify  an alias
* maps one human-readable hostname to another one that the browser should look up instead

## mx-records

* **MX records do not contain IP addresses, they container a pointer to a hostname.**
* The host pointed to by the MX record must have an A or AAAA record and cannot be a CNAME record.

```
abc.tv.      	A    		192.67.198.33
             	MX 5 		mail.abc.tv.
mail.abc.tv. 	A    		155.43.56.3
```

### cloudflare

To route emails to your mail server, you need to create two DNS records within Cloudflare:

1. An **A or AAAA** record for your mail subdomain that points to the IP address of your mail server.

```
Type	Name	IPv4 address	Proxy status
A			mail	192.0.2.1			Proxied
```

2. An **MX** record that points to that subdomain.

```
Type	Name	Mail server				TTL
MX		@			mail.example.com	Auto
```
