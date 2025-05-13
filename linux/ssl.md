# SSL

- **SSL:** Secure Sockets Layer; schiebt sich zwischen die Anwendungs- und Transportprotokollen; ssl-interception:
- **TLS**: Transport Layer Security; aktualisierte Version von SSL, die höhere Sicherheit bietet; Erweiterung von TCP um Verschlüsselung
- **HTTPS**: HTTP + SSL = Hyper Text Transfer Protocol Secure wird in der URL angezeigt, wenn eine Website durch ein SSL-Zertifikat abgesichert ist
- **Verschlüsselung**: öffentlicher Schlüssel (Zertifikat) + privater Schlüssel (RSA)
- **Signatur**: Authentifizierung (i.d.R.) des Servers gegenüber dem Client

# summary

* if I use a self-signed certificate I need to make exceptions for every website that is requested
  one for `127.0.0.1:4001`
  one for `https://resources.unonweb.local/custom-elements/`

# paths

```sh
ll /etc/ssl/ # system wide
```

# localhost (self-signed)

Anyone can make their own certificates without help from a CA. The only difference is that certificates you make yourself won’t be trusted by anyone else. For local development, that’s fine.

```sh
# user (let's encrypt)
mkdir ~/.ssl
openssl req -x509 -out ~/.ssl/localhost.crt -keyout ~/.ssl/localhost.key \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=localhost' -extensions EXT -config <( \
   printf "[dn]\nCN=localhost\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:localhost\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```

```sh
# system wide
sudo openssl req -x509 -out /etc/ssl/certs/localhost.crt -keyout /etc/ssl/private/localhost.key \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=localhost' -extensions EXT -config <( \
   printf "[dn]\nCN=localhost\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:localhost\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```

convert the client certificate + the private key into a PKCS12 certificate, for example:

```sh
openssl pkcs12 -export -inkey ~/.ssl/localhost.key -in ~/.ssl/localhost.crt -out ~/.ssl/localhost.p12
```

# localhost (own CA)

Step 1: Becoming your own CA (unfinished)

```sh
# Generate an RSA private key of size 2048:
openssl genrsa -des3 -out ~/.ssl/rootCA.key 2048
# Generate a root certificate valid for two years:
openssl req -x509 -new -nodes -key  ~/.ssl/rootCA.key -sha256 -days 730 -out  ~/.ssl/rootCA.pem
# To check just created root certificate:
openssl x509 -in ~/.ssl/rootCA.pem -text -noout
```

## mkcert

* https://github.com/FiloSottile/mkcert

```sh
sudo apt install libnss3-tools
sudo apt install mkcert

mkcert -install # Install the local CA in the system trust store.

mkcert example.com "*.example.com" example.test localhost 127.0.0.1 ::1
# Created a new certificate valid for the following names:
 - "example.com"
 - "*.example.com"
 - "example.test"
 - "localhost"
 - "127.0.0.1"
 - "::1"
 
mkcert unonweb.local "*.unonweb.local" localhost 127.0.0.1 ::1
# customize output paths:
mkcert -key-file ~/.ssl/mkcertkey.pem -cert-file ~/.ssl/mkcertcert.pem unonweb.local "*.unonweb.local" localhost 127.0.0.1 ::1

# ca file is stored:
/home/frida/.local/share/mkcert/rootCA
```

## certbot

* ACME Protocol client
* ACME (Automatic Certificate Management Environment) is a communication protocol which is designed for the Let’s Encrypt service to allow  everyone to issue the certificates easily.
* https://certbot.eff.org/

### install

```sh
# Install Certbot via Snaps
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot # ensure that the certbot command can be run
```

### default

```sh
# Stop your webserver
# Certbot will temporarily spin up a webserver on your machine:
sudo certbot certonly --standalone
```

### cmds

```sh
sudo certbot certificates # show 
sudo certbot delete --cert-name cms.unonweb.de

# Test renewal:
sudo certbot renew --dry-run
sudo systemctl status certbot.timer
```

### wildcard

```sh
# Install DNS CloudFlare plugin
sudo snap install certbot-dns-cloudflare
sudo snap set certbot trust-plugin-with-root=ok # Plugins will have the same classic containment as the Certbot snap

# Create a secrets directory
sudo mkdir /root/.secrets/
sudo touch /root/.secrets/cloudflare.ini

# Add credentials to secrets file
sudo nano /root/.secrets/cloudflare.ini


# a) Example credentials file using restricted API Token (recommended):
# Cloudflare API token used by Certbot
dns_cloudflare_api_token = 0123456789abcdef0123456789abcdef01234567

# b) Example credentials file using Global API Key (not recommended):
# Cloudflare API credentials used by Certbot
dns_cloudflare_email = cloudflare@example.com
dns_cloudflare_api_key = 0123456789abcdef0123456789abcdef01234

# Secure the secrets file
sudo chmod 0700 /root/.secrets/
sudo chmod 0400 /root/.secrets/cloudflare.ini

sudo certbot certonly --dns-cloudflare --dns-cloudflare-credentials /root/.secrets/cloudflare.ini -d unonweb.de,*.unonweb.de
```

```ini
Certificate is saved at: /etc/letsencrypt/live/unonweb.de/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/unonweb.de/privkey.pem
```

# protocols

- **SSL v2** is insecure and must not be used. This protocol version is so bad that it can be used to attack RSA keys and sites with the same name even if they are on an entirely different servers (the DROWN attack).
- **SSL v3** is insecure when used with HTTP (the SSLv3 POODLE attack) and weak when used with other protocols. It’s also obsolete and shouldn’t be used.
- **TLS v1.0** and **TLS v1.1** are legacy protocol that shouldn't be used, but it's typically still necessary in practice. Its major weakness (BEAST) has been mitigated in modern browsers, but other problems remain. TLS v1.0 has been deprecated by PCI DSS. Similarly, TLS v1.0 and TLS v1.1 has been deprecated in January 2020 by modern browsers. Check the SSL Labs blog link
- **TLS v1.2** and **v1.3** are both without known security issues; should be your main protocol because these version offers modern authenticated encryption (also known as AEAD)

# forward secrecy

is a protocol feature that enables secure conversations that are not dependent on the server’s private key. With cipher suites that do not provide forward secrecy, someone who can recover a server’s private key can decrypt all earlier recorded encrypted conversations. You need to support and prefer ECDHE suites in order to enable forward secrecy with modern web browsers. To support a wider range of clients, you should also use DHE suites as fallback after ECDHE. Avoid the RSA key exchange unless absolutely necessary. My proposed default configuration in Section 2.3 contains only suites that provide forward secrecy.