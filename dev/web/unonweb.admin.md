# WORKFLOW

## Creating a new user

### domain

1. Domain registrieren
2. Als zuständigen NameServer Netlify angeben.

### hosting

1. E-Mail-Alias `kunde@unonweb.de` anlegen 
   https://www.strato.de/apps/CustomerService
2. Netlify-Konto für Kunde anlegen:
   https://app.netlify.com/login/email

### unonweb server filesystem

* execute `init_site.sh` (/home/frida/code/web/projects/sites/11ty/_init_site)

### unonweb payloadcms

1. create new site
2. create new user
3. connect site with user

### unonweb apache config

```sh
sudo xed /etc/apache2/sites-available/haerer-geruestbau.local.conf
```

```html
<VirtualHost *:8000>
    ServerName domain.local
    DocumentRoot /home/frida/code/web/projects/sites/11ty/domain/_build
    Options +Indexes
</VirtualHost>
```

```sh
sudo a2ensite <site>
sudo systemctl reload apache2
```

### unonweb dns config

```sh
sudo xed /etc/hosts
```

# SERVER

## install

```sh
# node
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg

NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list

sudo apt-get update
sudo apt-get install nodejs -y
```

```sh
# mongodb
sudo apt-get install gnupg curl

curl -fsSL https://pgp.mongodb.com/server-7.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
   --dearmor
   
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

sudo apt-get update
sudo apt-get install -y mongodb-org
sudo systemctl start mongod
```

```sh
scp -P 15108 /home/payload/backup/db_231223.archive payload@strato:/home/payload/backup
```

## backup

### db

```sh
mongodump --archive="/home/payload/backup/db_231223.archive" mongodb://localhost:27017
scp -P 15108 /home/payload/backup/db_231223.archive payload@strato:/home/payload/backup
mongorestore --archive="/home/payload/backup/db_231223.archive" mongodb://localhost:27017
```

## updates

```sh
ssh -i /home/payload/.ssh/id_ed25519 payload@strato -p 15108
```

```sh
# rsync payload
rsync --progress --human-readable --archive --delete -e "ssh -p 15108" /home/payload/cms/ --exclude src --exclude performance.ods --exclude README.md --exclude _scripts --exclude .env --exclude .not_used --exclude _notes.md --exclude .vscode payload@strato:/home/payload/cms
```

```sh
# doesn't work
scp -P 15108 /etc/systemd/user/un-payload-prod.service payload@strato:/etc/systemd/user/un-payload-prod.service

rsync -e "ssh -p 15108" -p 15108 --rsync-path="sudo rsync" /etc/systemd/user/un-payload-prod.service payload@strato:/etc/systemd/user/un-payload-prod.service
```

# HOSTER

## strato

### Mail

-  IMAP-Server: imap.strato.de
-  POP-Server: pop3.strato.de 
- SMTP-Server: smtp.strato.de

#### SPF

```sh
TXT  unonweb.de  "v=spf1 redirect=_spf.strato.com
```

#### DKIM

https://blog.marvinscham.de/dkim-strato-cloudflare/

```sh
CNAME # typ
strato-dkim-0002._domainkey # name
strato-dkim-0002._domainkey.strato.de # target
```

#### DMARC

```sh
TXT # Typ
_dmarc # Präfix
v=DMARC1; p=quarantine; pct=100 # Value
```

## ionos

### Mail-Server

#### MX-Record

**mx00.ionos.de** und **mx01.ionos.de**

#### SPF

Wenn Sie IONOS-Postfächer nutzen und Ihre  DNS-Zone bei einem anderen Anbieter verwalten, müssen Sie den  SPF-Eintrag bei Ihrem DNS-Anbieter einrichten. Erstellen Sie dazu einen TXT-Record für Ihre Domain mit folgendem Wert:

```sh
v=spf1 include:_spf-eu.ionos.com ~all
```

#### DKIM

- https://www.ionos.de/hilfe/domains/mailserver-und-verwandte-records-konfigurieren/e-mail-authentifizierung-mit-dkim/

```sh
CNAME  s1-ionos._domainkey s1.dkim.ionos.com  Mail  
CNAME  s2-ionos._domainkey  s2.dkim.ionos.com  Mail
CNAME  s42582890._domainkey s42582890.dkim.ionos.com  Mail
```

für subdomain:

```
s1-ionos._domainkey.NAME_DER_SUBDOMAIN 	s1.dkim.ionos.com 	1 Stunde
s2-ionos._domainkey.NAME_DER_SUBDOMAIN 	s2.dkim.ionos.com 	1 Stunde
s42582890._domainkey.NAME_DER_SUBDOMAIN 	s42582890.dkim.ionos.com
```

```sh
haerer-geruestbau.de:s1-ionos # check 
```

## netlify

We recommend:

1. set up your primary site as a subdomain that uses the `www.` prefix (or `app.` or `docs.`, etc.) 
2. set up a CNAME which maps that site to the URL of your Netlify site (`<automatically-generated-name>.netlify.app`)

```sh
manueldieterich.de	3600	IN	MX	1	smtp.strato.de # mx record
manueldieterich.de	3600	IN	NETLIFY	manueldieterich.netlify.app
www.manueldieterich.de	3600	IN	NETLIFY	manueldieterich.netlify.app
```

two important facts about NETLIFY and NETLIFYv6 type records:

1. These records only connect domain names to IP addresses.
2. These records do not control which site the domain name is linked to (if any)

# DOMAINS

## haerer-geruestbau.de

* Mail: Ionos
* Mail-Test-Result: https://www.mail-tester.com/test-akc1l051b

| Type  | Name                 | Content                     |
| ----- | -------------------- | --------------------------- |
| CNAME | haerer-geruestbau.de | haerer-geruestbau.pages.dev |
| CNAME | www                  | haerer-geruestbau.pages.dev |

## MX

| Type | Name                 | Content       |
| ---- | -------------------- | ------------- |
| MX   | haerer-geruestbau.de | mx00.ionos.de |
| MX   | haerer-geruestbau.de | mx01.ionos.de |

## SPF

| Type | Name                 | Content                               |
| ---- | -------------------- | ------------------------------------- |
| TXT  | haerer-geruestbau.de | v=spf1 include:_spf-eu.ionos.com ~all |

## DKIM

| Type  | Name                                      | Content                  |
| ----- | ----------------------------------------- | ------------------------ |
| CNAME | domainkey.haerer-geruestbau.de            | s2.dkim.ionos.com        |
| CNAME | s42582890._domainkey.haerer-geruestbau.de | s42582890.dkim.ionos.com |



## unonweb.de

| Type      | Name       | Content        |
| --------- | ---------- | -------------- |
| **A**     | *          | 212.227.171.69 |
| **A**     | unonweb.de | 212.227.171.69 |
| **CNAME** | www        | unonweb.de     |

## MX

* MX: strato

| Type | Name       | Content         |
| ---- | ---------- | --------------- |
| MX   | *          | smtpin.rzone.de |
| MX   | unonweb.de | smtpin.rzone.de |

## SPF

| Type | Name       | Content                           |
| ---- | ---------- | --------------------------------- |
| TXT  | unonweb.de | "v=spf1 redirect=_spf.strato.com" |

## DKIM

| Type  | Name                        | Content                               |
| ----- | --------------------------- | ------------------------------------- |
| CNAME | strato-dkim-0002._domainkey | strato-dkim-0002._domainkey.strato.de |

## DMARC

| Type | Name                           | Content                                                      |
| ---- | ------------------------------ | ------------------------------------------------------------ |
| TXT  | _dmarc                         | v=DMARC1; p=quarantine; rua=mailto:support@unonweb.de; ruf=mailto:support@unonweb.de; fo=1; |
| TXT  | *._report._dmarc.reporting.com | v=DMARC1                                                     |

## zirkusprimel.de

| Type | Name            | Content                           |
| ---- | --------------- | --------------------------------- |
| TXT  | zirkusprimel.de | "v=spf1 redirect=_spf.strato.com" |

## manueldieterich.de

* DNS: cloudflare
* CDN: netlify
* MX: strato

| Type  | Name               | Content                       |
| ----- | ------------------ | ----------------------------- |
| CNAME | manueldieterich.de | apex-loadbalancer.netlify.com |
| CNAME | www                | manueldieterich.netlify.app   |
| MX    | manueldieterich.de | smtpin.rzone.de               |

