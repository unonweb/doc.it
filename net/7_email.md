# Providers

## Ionos

https://www.ionos.de/hilfe/e-mail/postmaster/ip-adressen-der-11-ionos-mailserver/

# IMAP

## IMAP & archive

* https://support.mozilla.org/en-US/questions/1323866

Imap mail accounts can download just headers or full copies and this can be done on a per folder basis. If only headers are downloaded then when you select to read an email, it is retrieved from the server and stored in a temp folder on computer to facilitate viewing. It is not stored in Thunderbird. This means it does not use up space on your computer for storage.  It also means you  cannot view any of those emails without internet access to the server.  The emails are stored on the server. If you were to look in the profile  folders for the imap account you would only see *.msf files which are an indexing file used by Thunderbird. It does not contain emails. The set up determining what is downloaded is set up in the imap Account  Settings > 'Synchronise & Storage' setting.

**If you need to perform a backup then you need to ensure you have  downloaded full copies**. This will show as mbox files (no extension) with same name as folder in the profile folders alongside the *.msf files.

# Authentication

Email authentication is the process performed by mail servers to establish that an incoming email is legitimate and not fraudulent. 

* Like doing an ID check: Does the identity of the sender indeed match the records associated with the server it’s sent from?

## Tools

- https://www.mail-tester.com
- https://unspam.email/

## SPF

**Sender Policy Framework**

* defines which IP addresses are allowed to send emails for a domain

Add SPF records for all domains you control, even if you don’t send emails from them. This will prevent criminals from hijacking your email  domain for fraudulent email activity.

## DMARC

**Domain-based Message Authentication Reporting and Conformance**

* provides insights for domain owners to analyze domain activity
* manage email infrastructure
* detect potential email deliverability issues or bad actors

Every time a mail server receives an email, it verifies the sender’s authenticity. If one of the checks fails, it acts based on the policies set out by the sender in the DMARC record. This could result in the  email being flagged or bounced.

### syntax

#### required:

- **v**: Identifies the record and must be listed at the beginning. The value is always DMARC1
- **p**: Identifies the policy chosen for your record, which is applied by  mailbox providers if your email fails DMARC authentication. There are  three policies to choose from:
  - **p=none**: This directs mail servers to take no action but allows you to track emails being sent
  - **p=quarantine**: This directs mailer servers to send the email to the spam folder if it  fails DMARC authentication, and tracks emails being sent
  - **p=reject**: This directs mail servers to reject any emails that fail DMARC checks, resulting in a bounce. It also allows the sender to track emails being  sent. 

```sh
# Example of a basic DMARC record:
v=DMARC1; p=none; rua=mailto:dmarc@yourdomain.com
```

```sh
v=DMARC1; # Version; specifies the protocol version; required tag

p=none;
p=quarantine;
p=reject; # Policy; specifies the policy that should be followed for the domain

sp=none; 
sp=quarantine;
sp=reject; # Subdomain policy; similarly to the p tag, sp specifies the policy that should be followed for subdomains.

pct=100 # Percentage; specifies the percentage of emails that should be subjected to filtering.

ruf=support@unonweb.de # Forensic report email address; defines the email address that forensic reports should be sent to.

rua=support@unonweb.de # Aggregate report email address; defines the email address that aggregate reports should be sent to.

aspf=strict 
aspf=relaxed # SPF alignment mode; defines whether SPF domain alignment should relaxed or strict

adkim=strict 
adkim=relaxed # DKIM alignment mode; defines whether DKIM domain alignment should relaxed or strict

fo= # Forensic reporting options; defines how forensic reports are created and given to users
rf= # Report format; defines the forensic reporting format
ri= # Report interval; change the frequency of your reports
```

### reports

They are typically sent once every 24 hours, but you can change the frequency of your reports by using the `ri` tag in your DMARC record, accompanied by the desired number of seconds  between reports. To receive DMARC reports directly to your email  address, you must add the **rua** tag to your DMARC record, along with the email address that you would like reports to be sent to. Configuring your DMARC record in this way will let mailbox providers  know that they must send a report.

Every time an email fails an SPF and/or DKIM check, feedback will be given by the mailbox provider about the status of the message. This  means that DMARC reporting can provide crucial information about the  state of your outgoing emails, allowing you to identify malicious actors and block them.

## DKIM

**Domainkeys Identified Mail**

* Der sendende Mailserver fügt der E-Mail eine digitale Signatur hinzu
* Der empfangenden Mailserver überprüft diese Signatur. Dazu ruft er bei dem Mailserver, der als Absender deklariert ist, den zur Signatur passenden öffentlichen Schlüssel ab. Passt der öffentliche Schlüssel nicht zur Signatur, kann dies folgende Gründe haben:
  * Die E-Mail wurde nicht vom Mailserver gesendet, der im Mail-Header deklariert ist, sondern von einem anderen – betrügerischen – Server.
  * Die E-Mail wurde auf dem Weg vom „echten“ Mailserver zum Empfänger verändert. Beispielsweise könnte ein Hacker die Mail abfangen, abändern und anschließend weitersenden.

### Ionos

Um DKIM mit IONOS-Postfächern zu nutzen und deren DNS-Zone bei einem anderen Anbieter verwaltet wird, müssen Sie  die folgenden CNAME-Records bei Ihrem DNS-Anbieter einrichten:

```ini
CNAME  s1-ionos._domainkey s1.dkim.ionos.com  Mail  
CNAME  s2-ionos._domainkey  s2.dkim.ionos.com  Mail
CNAME  s42582890._domainkey s42582890.dkim.ionos.com  Mail
```

```sh
haerer-geruestbau.de:s1-ionos # check 
# s1-ionos = Selector

## Record value:
v=DKIM1; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA3PgemRMZHYwtxmmJV7gL8+O706Dvpd6z1b9dhvV9EcxoqgDIJfvXje6C+SZqTbLpYt3ZNbUuF1a3RZmueN4gfoSSx42FeW/5raMlHnxSGWSTh1srK7kcfphAJVpjNIilbdmXZEeTIPb7SwhaS+IQ2I7gY06ZNxYFrMoZGh1rH70W8+vMFqpMfSRouM2LjyKaH6r0+ptJ4rl6LOAi+aXyO0IA4qYt6s7GELWiIG6/cRmGab3y6vUDYk38t5trjYeh1DZSsR5MpsPg3uojXHVfshondy39yKD5LvI/bZm3eghyaC2ZKwkpt8hDJlolKpUlbMBYAAaUMQDRB+VH2j8/2QIDAQAB
```

### Funktionsweise

#### Hashing

Nach einem bestimmten Algorithmus wird aus den Inhalten von E-Mails eine Zeichenfolge – der sogenannte Hash-Wert – errechnet und anschließend dem Header der E-Mail hinzugefügt. Errechnet der Empfänger den Hash-Wert der erhaltenen E-Mail nach demselben Algorithmus, dann muss er muss er genau die Zeichenfolge erhalten, die dem Mail-Header hinzugefügt wurde. Stimmt der Hash-Wert nicht überein, weiß der Empfänger, dass die E-Mail verändert wurde.

#### Asymmetrische Verschlüsselung

Damit der Empfänger sicher sein kann, dass der Hash-Wert tatsächlich vom originalen Sender stammt, ist eine weitere Maßnahme erforderlich: die digitale Signatur. Zur Absender-Authentifizierung wird zu diesem Zweck die asymmetrische Verschlüsselung verwendet. Sie basiert auf einem Schlüsselpaar: Was mit Schlüssel A verschlüsselt wurde, kann nur mit Schlüssel B wieder entschlüsselt werden. Während ein Schlüssel geheim gehalten wird („privater Schlüssel“), ist der andere öffentlich („öffentlicher Schlüssel“).

Der Ablauf sieht folgendermaßen aus:

1. Der Absender verschlüsselt den errechneten Hash-Wert mit dem privaten Schlüssel.
2. Den verschlüsselten Hash-Wert fügt er dem Mail-Header in Form der digitalen Signatur hinzu.
3. Der Empfänger ruft auf dem DNS-Server des Senders dessen öffentlichen Schlüssel ab und entschlüsselt damit die Signatur.
4. Der Empfänger rechnet den entschlüsselten Hash-Wert nach. Stimmt der selbst errechnete Hash-Wert mit dem entschlüsselten überein, ist alles in Ordnung.

#### TXT-Eintrag auf dem Nameserver

Damit die empfangenden Mailserver den öffentlichen Schlüssel des  Senders abrufen können, muss dieser nach der DKIM-Einrichtung als TXT-Record in der DNS-Zone der Domain veröffentlicht werden.

Der DKIM-Eintrag enthält daher die folgenden Elemente:

- Die Version, häufig codiert mit `v=DKIM1`
- Den Verschlüsselungsalgorithmus; dieser ist immer RSA (`k=rsa`).
- Der öffentliche Schlüssel (`p=`); dabei handelt es sich um eine lange Zeichenkette.
- Der Selektor; dieser ist je nach Provider unterschiedlich. Beispiel: `default._domainkey` oder `k1._domainkey`.