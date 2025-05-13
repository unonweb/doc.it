# ID(P)S

**Intrusion Detection (/Prevention) System**; 

- **IDS**: monitor network traffic and system activities for malicious activity  
- **IPS**: prevent or block intrusions that are detected

# SIEM

SIEM is a fairly common acronym in computing, one you’re bound to come across in the world of network security tools. It’s short for “***security information and event management***.” In basic terms, SIEM technology uses a combination of security information management (SIM) and security event management (SEM) tools.

# TYPES

## detection

### signature-based

#### host-based

Similarly to Antivirus Apps signature based HIDSs search for bit patterns or keywords in program files by performing similar scans on log files.

#### network-based

Function more like firewalls, except where firewalls run checks on keywords, packet types, and protocol activity entering or exiting the network, NIDSs run similar checks on traffic moving within the network.

### anomaly-based

Looks for unusual or irregular activity caused by users or processes. According to the pattern of normal traffic behavior; the IPS performs a statistical analysis and establishes a comparison standard. When the traffic moves too far from this standard, it sends out an alert.

If your network was accessed with the same login credentials from several different cities around the globe all in the same day, it could be a sign of anomalous behavior.

A HIDS using anomaly-based detection surveys log files for indications of unexpected behavior, while a NIDS monitors for the anomalies in real time.

## position

### host based (HIDS)

log file scanner

- tend to have more capabilities as they have access to the entire host:

  can look at network traffic as well as monitor files and logs.
  can identify file permission changes and unusual client-server requests

- check logged files for evidence of malicious activity

- allow you to examine historical data for patterns of activity

- highly effective against insider threats.  

- often used for mission-critical machines that are not expected to change.

### network based (NIDS)

packet sniffer

- work more in real time, tracking live data for signs of tampering
- allow for faster response times to potential security threats, as real-time packet data monitoring can trigger alerts if something suspicious occurs

# HOST BASED

work by collecting data about events taking place on the system being monitored.  This data is recorded by operating system mechanisms called ***audit trails***. As host-based systems rely heavily on audit trails, they become limited by these audit trails. The fact that audit trails are protected by the operating systems itself offers some assurance that audit trails have not been improperly modified.

The problem is that, as the sensors gather finer levels of detail, they accumulate large amounts of data that take up significant storage.  In addition, because, “***both the volume and complexity of the data rise with greater detail ...it makes it difficult for an adversary to circumvent the audit process entirely, the greater volume and complexity of the data make it easier in practice for intruders to hide their footprints***”.  This sort of irony becomes the burden that designers and analysts must overcome so that host-based sensors avoid becoming cumbersome, while remaining effective.

#### advantages

trace malicious or improper activities to a specific user ID 

## tripwire

A Tripwire check compares the current filesystem state against a known baseline state, and alerts on any changes it detects. The baseline and check behavior are controlled by a policy file, which specifies which files or directories to monitor, and which attributes to monitor on them, such as hashes, file permissions, and ownership.

When an expected change occurs, such as upgrading a package, the baseline database can be updated to the new known-good state. The policy can also be updated, for example to reduce noise or cover a newly installed package. 

## ossec

combines tools like tripwire and fail2ban together into a single tool. It can be centrally managed and uses encrypted tunnels to communicate with clients.

- it also offers a few system monitoring tools you’d be more likely to find in a NIDS.
- it's an incredibly efficient processor
- it doesn’t have a user interface. You can get around this by running an open-source dashboard like Kibana or Graylog.
- it organizes and sorts your log files and uses anomaly-based detection strategies and policies

#### key features

- File Integrity checking
- Log Monitoring
- Rootkit detection
- Active response

# NETWORK BASED

## snort

**NIDS/NIPS** (network based; can act as an **IDS** by mirroring traffic to the system, or as an **IPS** by putting it in-line with traffic)

lauscht direkt an der Netzwerkschnittstelle und vergleicht den Traffic mit verdächtigen Mustern (Signaturen) (vgl. Virenscanner);

#### modes

- **sniffer mode**: simply reads the packets off of the network and displays them for you in a continuous stream on the console (like tcpdump)
- **packet logger mode**: logs the packets to disk
- **Network Intrusion Detection System** (NIDS) **mode**: performs detection and analysis on network traffic; the most complex and configurable mode

### commands

#### sniffer mode

problem: it's slow; if doing IDS work with Snort, don't use the verbosed sniffing mode, you will drop packets

```sh
snort -v # dumps TCP/IP packet headers 
snort -d # dumps the application layer data 
snort -e # dumps/log the link layer packet headers
```

#### sniffer mode

default logging directory = /var/log/snort/

```sh
snort -K pcap|ascii|none # logging-mode; default = pcap
snort -h <ip>/<mask> # once set, all decoded packet logging will be done relative to the home network address space (all decoded output will be logged into decode directories with the address of the foreign computer as  the directory name)
snort -c <config-file> # use the rules located in *config-file*
```

## suricata

**IDS/IPS**, released in 2009. Suricata is designed to be multi-threaded, making it much faster than competing products. Like Snort, it uses signatures and heuristic detection. In fact, it can use most Snort rules without any changes

## zeek

Formerly Bro; Bro was first release in 1994, making it one of the oldest IDS applications mentioned here. Originally named in reference to George Orwell's book, 1984, more recent times have seen a re-branding to the arguably less offensive name, Zeek.

Zeek is often used as a network analysis tool but can also be deployed as an IDS. Like Snort, Zeek uses *libpcap* for packet capture. Once packets enter the system, however, any number of frameworks or scripts can be applied. This allows Zeek to be very flexible. Scripts can be written to be very specific, targeting specific types of traffic or scenarios, or Zeek can be deployed as an IDS, using various signatures to identify and report on suspect traffic.

# MAPWOC

**massive automated passive Web Observation Center**; dient zur automatisierten Überprüfung der Integrität von Webseiten und der Erkennung böswillig verfälschter Inhalte, Schutz gegen **Drive-By-Downloads**