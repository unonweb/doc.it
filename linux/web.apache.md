# install

```sh
sudo apt install apache2
```

# files

```sh
xed /etc/apache2/apache2.conf # Hauptkonfigurationsdatei
xed /etc/apache2/ports.conf # hier werden listening ports festgelegt
ll /etc/apache2/mods-enabled/ # verwaltet von a2enmod bzw. a2dismod
ll /etc/apache2/conf-enabled/ # verwaltet von a2enconf bzw. a2disconf
ll /etc/apache2/sites-enabled/ # Konfiguration der Virtual Hosts; verwaltet von a2ensite bzw. a2dissite
```

# CLI

## apache2ctl

```sh
apache2ctl start #
apache2ctl stop
apache2ctl restart # restarts by SIGHUP; automatically checks the configuration files via configtest

sudo systemctl start apache2 # the same as "apache2ctl start"
sudo systemctl reload apache2

apache2ctl graceful stop # open connections are not aborted
apache2ctl graceful # restarts; open connections are not aborted; designed to always respect the process control directive of the MPM, so the number of processes and threads available to serve clients will be maintained at the appropriate values throughout the restart process.
```

```sh
sudo apache2ctl configtest # runs a configuration file syntax test
apache2ctl -S
apache2ctl status # brief status report
apache2ctl fullstatus # report from mod_status (must be enabled on your server); requires a text-based browser such as lynx available on your system; the URL used to access the status report can be set by setting the APACHE_STATUSURL variable in /etc/apache2/envvars
sudo systemctl status apache2.service
```

## a2ensite

```sh
# a2ensite/a2dissite
a2ensite <site> # enable site
a2dissite <site> # disable site (virtual host) by creating/removing symlinks within /etc/apache2/sites-enabled
```

### troubles

* ERROR: Site doc.unonweb.de does not exist!
  solution: adding `.conf` extension to site configuration files.

## a2enmod

```SH
# a2enmod/a2dismod
a2enmod <module> # enalbe
a2dismod <module> # disable module by creating/removing symlinks within /etc/apache2/mods-enabled
# in addition to the .load file, many modules have an associated .conf file
# enabling the module puts the configuration directives in the .conf file as directives into the main server context
sudo a2enmod ssl
sudo a2enmod headers
sudo systemctl restart apache2
```

## a2query

```sh
# a2query
# retrieve configuration values from a local Apache 2 server
a2query -m # listet alle aktiven Module
a2query -v # returns the currently installed Apache 2 server version
```

# info

If the `Listen` specified in the configuration file is default of 80 (or any other port below 1024), then it is necessary to have root privileges in order to start apache, so that it can bind to this privileged port.

Once the server has started and performed a few preliminary activities such as opening its log files, it will launch several child processes which do the work of listening for and answering requests from clients. The main httpd process continues to run as the root user, but the child processes run as a less privileged user. This is controlled by the selected *Multi-Processing Module*.

You will notice many httpd executables running on your system, but you should not send signals to any of them except the parent, whose pid is in the PidFile.

# log

```sh
# apache
tail -f /var/log/apache2/access.log
tail -f /var/log/apache2/error.log
journalctl /usr/sbin/apache2 -f
```

```sh
# grep access.log
egrep -o "\% [0-9]+ " logs/access.log | cut -b3- | tr -d " " | sort -n # 
```

# CONFIG

```sh
Listen 80
Listen 8000

DocumentRoot <path> # setzt das Wurzel-Verzeichnis, von dem aus httpd Dateien ausliefert; der Server hängt Pfade aus der angeforderten URL an das Wurzelverzeichnis an, um den Pfad zum Dokument zu bilden; wenn das Verzeichnis nicht absolut angegeben ist, wird es relativ zu ServerRoot betrachtet.  

ServerSignature On|Off|Email # [all: core] default: off; Konfiguriert die Fußzeile von servergenerierten Dokumenten; hilfreich, um bei einer Kette von Proxy-Servern zu erkennen, welcher der verketteten Server gegenwärtig die zurückgegebene Fehlermeldung produziert hat.

ServerRoot <path> # Basisverzeichnis der Serverinstallation (Konfigurations- und Logdateien); do not add a slash at the end of the directory path.

User <userid> # sets the user ID as which the server will answer requests. In order to use this directive, the server must be run initially as root. If you start the server as a non-root user, it will fail to change to the lesser privileged user, and will instead continue to run as that original user. If you do start the server as root, then it is normal for the parent process to remain running as root.

# The user should have no privileges that result in it being able to access files that are not intended to be visible to the outside world, and similarly, the user should not be able to execute code that is not meant for HTTP requests. It is recommended that you set up a new user and group specifically for running the server. Some admins use user nobody, but this is not always desirable, since the nobody user can have other uses on the system.
```

## context

- Direktiven sind nur in bestimmten Kontexten verfügbar: **server**, **vhost**, **directory**, **.htaccess**
- Everything that is allowed in `<Directory>` sections is also allowed in  
  - `<DirectoryMatch>`
  - `<Files>`
  - `<FilesMatch>`
  - `<Location>`	
  - `<LocationMatch>`
  - `<Proxy>`
  - `<ProxyMatch>`

### filesystem containers

```html
<Directory path> # Direktiven, die nur auf das genannte Verzeichnis und dessen Unterverzeichnisse angewendet werden; shell-style wildcards are allowed

<DirectoryMatch regex> # 
<Files filename> # apply to any file with the specified name, regardless of what directory it lies in; können innerhalb von <Directory> und (anders als <Directory> und <Location>) innerhalb von .htaccess-Dateien verwendet werden; shell-style wildcards are allowed

example: the following configuration directives will, when placed in the main section of the configuration file, deny access to any file named private.html regardless of where it is found

<Files "private.html">
	Require all denied
</Files>

<FilesMatch regex> # enthält Direktiven, die für Dateinamen gelten, die auf einen regulären Ausdruck passen; Beispiel: <FilesMatch "\.(gif|jpe?g|png)$"> für die gebräuchlichsten Grafikformate
```

### webspace containers

```html
<Location URL> # Direktiven, die nur auf die angegebene URL angewendet werden; nicht geeignet den Zugriff zu Teilen des Dateisystems zu steuern; <Location /> wendet eine Konfiguration auf den gesamten Server an; shell-style wildcards are allowed

<LocationMatch regex> # wendet die enthaltenen Direktiven nur auf URLs an, die auf reguläre Ausdrücke passen
```

The following configuration prevents access to any URL-path that begins in /private:

```html
<LocationMatch "ˆ/private">
	Require all denied
</LocationMatch>
```

### virtual hosts

Direktiven, die nur auf bestimmte IP-Adressen angewendet werden; 

```html
<VirtualHost ip[:port]>
</VirtualHost>
```

* `*` erfasst alle IP-Adressen bzw. Ports; 
* der Default Port sowie die IP-Adresse, an der Apache lauscht, wird mit `Listen` festgelegt 

## virtual hosts

* refers to the practice of running more than one web site (such as `manu.example.com` and `radja.example.com`) on a single machine. 
* Virtual hosts can be *IP-based*, meaning that you have a different IP address for every web site, or *"name-based"*, meaning that you have multiple names running on each IP address. 
* The fact that they are running on the same physical server is not apparent to the end user

### how apache selects the virtual host

1. When a request arrives, the server will find the best (most specific) matching `<VirtualHost>` argument based on the *IP address* and *port* used by the request.
2. If there is more than one virtual host containing this best-match address and port combination, Apache will further compare the `ServerName` and `ServerAlias` directives to the server name present in the request.

### ip-based

* using a single IP address but multiple TCP ports, is also IP-based virtual hosting (in apache-terminology)

```html
<VirtualHost 172.20.30.40:80>
  ServerName www1.example.com  
  ServerAdmin webmaster@www1.example.com
  DocumentRoot "/www/vhosts/www1"
  ErrorLog "/www/logs/www1/error_log"
  CustomLog "/www/logs/www1/access_log" combined
</VirtualHost>

<VirtualHost 172.20.30.50:80>
    ServerAdmin webmaster@www2.example.org
    DocumentRoot "/www/vhosts/www2"
    ServerName www2.example.org
    ErrorLog "/www/logs/www2/error_log"
    CustomLog "/www/logs/www2/access_log" combined
</VirtualHost>
```

### name-based

```html
<VirtualHost *:80>
    # This first-listed virtual host is also the default for *:80
    ServerName www.example.com
    ServerAlias example.com 
    DocumentRoot "/www/domain"
</VirtualHost>

<VirtualHost *:80>
    ServerName other.example.com
    DocumentRoot "/www/otherdomain"
</VirtualHost>
```

### ServerName

* sets the request scheme, hostname and port that the server uses to identify itself. This is used when creating redirection URLs.
* In the context of virtual hosts, specifies what hostname must appear in the request's *Host:* header to match this virtual host. For the default virtual host this value is not decisive as it is used as a last resort host regardless. However, you must set it for any further virtual host explicitly.

```html
<VirtualHost *:80> 
	ServerName fqdn[:port]
  ServerName manueldieterich
  + Indexes
</VirtualHost>
```

`ServerAlias hostname [hostname] ...` # Alternativer Name für einen Host, der verwendet wird, wenn Anfragen einem namensbasierten virtuellen Host zugeordnet werden.  

## directory access

```html
<VirtualHost *:80> 
  ServerName manueldieterich
  Options +Indexes
</VirtualHost>
```

### options

`Options [+|-]option …` # Definiert, welche Eigenschaften oder Funktionen in einem bestimmten Verzeichnis verfügbar sind; fügt eine oder mehrere Optionen hinzu bzw. zieht sie ab.

* `All`             # alle Optionen außer MultiViews; default
* `ExecCGI`         # die Ausführung von CGI-Skripten, welche mod_cgi verwenden, ist erlaubt.
* `FollowSymLinks`  # der Server folgt symbolischen Links in diesem Verzeichnis. Auch wenn der Server symbolischen Links folgt, bedeutet dies nicht, dass der zum Abgleich gegen `<Directory>`-Abschnitte verwendete Pfadname wechselt. Beachten Sie auch, dass diese Option innerhalb eines `<Location>`-Abschnitts ignoriert wird.
* `Includes`        # Server Side Includes, die von mod_include bereitgestellt werden, sind erlaubt.
* `IncludesNOEXEC`  # Server Side Includes sind erlaubt, #exec cmd und #exec cgi sind jedoch deaktiviert. Es ist aber noch möglich, CGI-Skripte aus ScriptAlias-Verzeichnissen mittels #include virtual einzubinden.
* `Indexes`         # Wenn eine URL, die auf ein Verzeichnis zeigt, in dem sich keine durch DirectoryIndex definierte Indexdatei (z.B. index.html) befindet, dann liefert mod_autoindex eine formatierte Auflistung des Verzeichnisses zurück.
* `MultiViews`      # "MultiViews" sind bei der Verwendung von mod_negotiation erlaubt (siehe Content-Negotiation).
* `SymLinksIfOwnerMatch` # Der Server folgt nur symbolischen Links, bei denen die Zieldatei bzw. das Zielverzeichnis der gleichen Benutzerkennung gehört, wie der Link.

#### default setting for everywhere

```html
<Directory />
	Options None
	AllowOverride None
	Require all denied
</Directory>
```
`<Directory />` # doesn't actually refer to the document root, it refers to the filesystem root. So creating a `<Directory />` block is a way to specify directives that apply to all files and directories anywhere on the server. It basically serves as a default setting. The idea is, you don't want someone to be able to hack your server by, for example, somehow uploading an .htaccess file, just because you forgot to disable .htaccess files in some directory. So you start by disabling .htaccess files everywhere, then you don't have to worry about it. You can still enable them where you need them with AllowOverride directives for specific directories.

## authorization

`Require [not] ...` # tests whether an authenticated user is authorized according to a particular authorization provider and the specified restrictions; listet several times require any of them

* `all granted|denied` # access allowed/denied unconditionally (mod_authz_core)
* `method <http-method>` [http-method] # access is allowed only for the given HTTP methods (mod_authz_core)
* `expr <expression>` # access is allowed if expression evaluates to true (mod_authz_core)
* `valid-user` # all valid users can access the resource
* `group <group> [<group]` # only users in the named groups can access the resource.
* `ip <ip[/mask]>` # limit access to clients with the specified IP address (range); (mod_authz_host)
* `host <host>` # a (partial) domain-name (mod_authz_host)

### allow access to directory

```html
<DirectoryMatch "/home/frida/code/web/resources/components/native_light">
    Require all granted
</DirectoryMatch>
```

### disable .htaccess

In a default installation, users can override apache configuration using *.htaccess*. If you want to stop users from changing your apache server settings, you can add *AllowOverride* to *None* as shown below. This must be done at the root level.

```html
<Directory /> 
	AllowOverride None
</Directory>
```

## log

```sh
# error log
ErrorLog /var/log/httpd/error.log # filepath to which the server will log any errors
LogLevel <lvl> # control which messages are logged to the errorLog
# trace8, ..., trace1, debug, info, notice, warn, error, crit, alert, emerg
LogLevel info ssl:warn # It is also possible to configure the log level for particular modules (ssl)

# access log
LogFormat "%h %l %u %t \"%r\" %>s %b" common # defines the log format named "common"
CustomLog logs/access.log common # writes a custom log to this path using the "common" format
```

### CustomLog

* https://httpd.apache.org/docs/2.4/mod/mod_log_config.html#customlog

```sh
CustomLog logs/access.log common # writes a custom log to this path using the "common" format
```

### LogFormat

* https://httpd.apache.org/docs/2.4/mod/mod_log_config.html#formats

```sh
%h # Remote hostname
%l # Remote logname
%u # Remote user if the request was authenticated

%v # The canonical ServerName of the server serving the request.

%t # Time the request was received

%f # Filename
%q # Query string
%r # First line of request
%s # Status
%U # The URL path
```

The following directives define some format nicknames for use with `CustomLog`

```sh
LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\"" unonweb_1

LogFormat "%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" vhost_combined
# haerer-geruestbau.unonweb.local:443 127.0.0.1 - - [13/Dec/2023:18:21:52 +0100] "GET /de/ HTTP/1.1" 200 5746 "https://haerer-geruestbau.unonweb.local/de/hier-finden-sie-uns/" "Mozilla/5.0 (X11; Linux x86_64; rv:120.0) Gecko/20100101 Firefox/120.0"

LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
LogFormat "%h %l %u %t \"%r\" %>s %O" common
# 127.0.0.1 - - [13/Dec/2023:18:21:08 +0100] "GET /de/ HTTP/1.1" 200 4193
LogFormat "%{Referer}i -> %U" referer
LogFormat "%{User-agent}i" agent
```

## core

* https://httpd.apache.org/docs/current/mod/core.html

# mod_headers

```sh
Header <condition> <action> "header"
```

## examples

```sh
Header always set Strict-Transport-Security max-age=31536000
Header always set Access-Control-Allow-Origin "*"
Header always add Access-Control-Allow-Origin "example1.com";
Header always add Access-Control-Allow-Origin "example2.com";
Header always add Access-Control-Allow-Origin "example3.com";
Header always set Access-Control-Allow-Methods "GET,REPORT"
Header always set Access-Control-Allow-Headers "Content-Type"

Header always set Content-Security-Policy "frame-ancestors '*'"
```

## condition

```sh
Header onsuccess unset X-Foo # can be omitted since it is the default
Header always set X-Foo "baz" # headers are added to the response even on error
```

## action

### add

```sh
Header always add Access-Control-Allow-Origin "https://resources.unonweb.local/"
# The response header is added to the existing set of headers, even if this header already exists. This can result in two (or more) headers having the same name. This can lead to unforeseen consequences, and in general set, append or merge should be used instead.
```

### set

```sh
Header always set Access-Control-Allow-Origin "https://resources.unonweb.local/" 
# The response header is set, replacing any previous header with this name. The value may be a format string.
```

### append

```sh
Header always append Access-Control-Allow-Origin "https://resources.unonweb.local/" # The response header is appended to any existing header of the same name. When a new value is merged onto an existing header it is separated from the existing header with a comma. This is the HTTP standard way of giving a header multiple values.
```

### merge

```sh
Header always merge Access-Control-Allow-Origin "https://resources.unonweb.local/" # The response header is appended to any existing header of the same name, unless the value to be appended already appears in the header's comma-delimited list of values. When a new value is merged onto an existing header it is separated from the existing header with a comma. This is the HTTP standard way of giving a header multiple values. Values are compared in a case sensitive manner, and after all format specifiers have been processed. Values in double quotes are considered different from otherwise identical unquoted values.
```



```sh
# You can put this directive in the root of your configuration to apply site-wide, but a better method is to apply the settings depending on the type of file. For example, to set a high TTL for most static media, you can use a FilesMatch block:

<FilesMatch ".(ico|pdf|flv|jpg|jpeg|png|gif|js|css|swf)$">
	Header set Cache-Control "max-age=63072000, public"
</FilesMatch>
```

# mod_rewrite

* https://httpd.apache.org/docs/current/rewrite/remapping.html
* https://httpd.apache.org/docs/current/rewrite/flags.html # flags

## rewrite vs redirect

A **rewrite** happens solely on the server, the client (browser) is blind to it. The browser sends a request and gets content.

A redirect is a server **response** to a request, that  tells the client (browser) to submit a new request. The browser asks for a url, the server gets that  request and **responds with a redirect**, the browser gets the response and loads the URL in the server's response. The URL in the location bar is now the new URL and the browser sends a request for the new URL.

## syntax

```sh
RewriteRule pattern target [Flag1,Flag2,Flag3]
[NC][nocase] # 
[PT]passthrough
[R][redirect]
```

## examples

```sh
# Redirect Requests to SSL
RewriteEngine on
RewriteCond %{HTTP_HOST} ^agubint.zipol.net [NC]
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```



```sh
#### [agubint]

# artetucan
RewriteCond %{HTTP_HOST} ^artetucan.zipol.net [NC]
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L]

# spanisch
RewriteCond %{HTTP_HOST} ^spanisch.zipol.net [NC]
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
```

```sh
RewriteEngine On
RewriteCond %{HTTP_HOST} ^oli.zipol.net [NC] # requests to http://oli.zipol.net
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L] # are forwarded to https://oli.zipol.net
```

```sh
RewriteEngine On
RewriteCond %{HTTP_HOST} ^zipol.net [NC] # requests to http://zipol.net [no case]
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L] # are forwarded to https://zipol.net
```

```sh
# nextcloud calendar
RewriteEngine On
RewriteRule    "zollernalternativ.unonweb.de"  "https://nextcloud.unonweb.de/index.php/apps/calendar/p/c8RdG2YFF56wZNbW" [PT]
```

# mod_security

* https://modsecurity.org/

```sh
# get repo version
sudo apt install libapache2-mod-security2
```

```sh
sudo cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
sudo nano /etc/modsecurity/modsecurity.conf
```

```sh
SecRuleEngine DetectionOnly --> SecRuleEngine On
```

## OWASP Core Rule Set

* https://coreruleset.org/docs/deployment/install/
* https://github.com/coreruleset/coreruleset/releases
* ModSecurity v3 is used in production together with Nginx, but the CRS project recommends to use the ModSecurity v2 release line with Apache.

### setup

The order of file inclusion in your webserver configurationshould always be:

1. `modsecurity.conf`
2. CRS configuration: `crs-setup.conf`
3. Plugin configuration
4. Plugin rules before CRS rules
5. CRS rules
6. Plugin rules after CRS rules

**download and move core-rule-set**

```sh
cd /tmp
wget https://github.com/coreruleset/coreruleset/releases/download/v4.7.0/coreruleset-4.7.0-minimal.tar.gz --output-document coreruleset.tar.gz

tar -xzf coreruleset.tar.gz

sudo cp -r coreruleset-4.7.0/ /etc/modsecurity/
sudo  mv /etc/modsecurity/coreruleset-4.7.0/crs-setup.conf.example /etc/modsecurity/coreruleset-4.7.0/crs-setup.conf
```

**download nextcloud-rule-exclusions-plugin**:

* https://github.com/coreruleset/nextcloud-rule-exclusions-plugin/releases/tag/v1.2.0
* https://coreruleset.org/docs/concepts/plugins/#how-to-install-a-plugin

**load the CRS config files in apache:**

* order matters!

```sh
# Edit your Apache security2.conf file to ensure it’ll load ModSecurity rules:
sudo nano /etc/apache2/mods-enabled/security2.conf
```

```html
<IfModule security2_module>                                             
    # Default Debian dir for modsecurity's persistent data
    SecDataDir /var/cache/modsecurity                                                                                                                                                            
    Include /etc/modsecurity/modsecurity.conf
    Include /etc/modsecurity/crs/plugins/nextcloud-rule-exclusions/nextcloud-rule-exclusions-config.conf
    Include /etc/modsecurity/crs/plugins/nextcloud-rule-exclusions/nextcloud-rule-exclusions-before.conf                                                                                         
    # CRS rules                                          
    Include /etc/modsecurity/coreruleset-4.7.0/crs-setup.conf                                   
    Include /etc/modsecurity/coreruleset-4.7.0/plugins/*-config.conf                                    
    Include /etc/modsecurity/coreruleset-4.7.0/plugins/*-before.conf                                             
    Include /etc/modsecurity/coreruleset-4.7.0/rules/*.conf                                    
    Include /etc/modsecurity/coreruleset-4.7.0/plugins/*-after.conf                                                                                             
</IfModule>
```

## config

```sh
SecDataDir /tmp/
SecAuditLog /var/log/apache2/modsec_audit.log
```

## logs

```sh
sudo tail -f /var/log/apache2/modsec_audit.log
```

## test

```sh
curl https://nextcloud.unonweb.de/?foo=/etc/passwd&bar=/bin/sh
```

# mod_alias

## redirect

### redirect to https

```html
<VirtualHost *:80>
  ServerName site.mozilla.org
	Redirect permanent / https://site.mozilla.org/
</VirtualHost>
<VirtualHost *:80>
	Redirect /oldlocation https://www.newdomain.com/newlocation
</VirtualHost>
```

If the Redirect directive is used within a `<Location>` or `<LocationMatch>` section with the URL-path omitted, then the URL parameter will be interpreted using **expression syntax**. Furthermore you will have access to variables from *Mod_Rewrite*:

```html
<VirtualHost *:80>
	<Location "/">
		Redirect permanent "https://%{HTTP_HOST}%{REQUEST_URI}"
 	</Location>
</VirtualHost>
```

## redirectMatch

* equivalent to `Redirect`, but makes use of regular expressions, instead of simple prefix matching.

```html
RedirectMatch 301 /Dir/Perl/.* http://www.devdaily.com/perl
```

- `.` means "any character"
- `*` means "zero or more of the preceding character"

# ssl

```sh
# create ssl key
mkdir ~/.ssl
openssl req -x509 -out ~/.ssl/localhost.crt -keyout ~/.ssl/localhost.key \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=localhost' -extensions EXT -config <( \
   printf "[dn]\nCN=localhost\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:localhost\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```

```html
<VirtualHost *:443>
	SSLEngine on
	SSLProtocol +TLSv1.2
	SSLProtocol +TLSv1.3
	SSLCertificateFile /home/frida/.ssl/localhost.crt
	SSLCertificateKeyFile /home/frida/.ssl/localhost.key
</VirtualHost>
```

more ssl options

```html
<VirtualHost *:443>
	SSLCipherSuite [protocol] cipher-spec <!-- Auflistung der kryptographischen Algorithmen, der der Server für den SSL Handshake verwenden darf; Separator: Doppelpunkt; openssl ciphers -v provides a nice way to successively create the correct cipher-spec string -->
	SSLCACertificateFile path <!-- sets the all-in-one file where you can assemble the Certificates of CAs whose clients you deal with; enthält die Vertrauenskette der CAs, die die Zertifikate für die Clients ausgestellt haben -->
	SSLCACertificatePath <path> <!-- sets the directory where you keep the Certificates of CAs whose clients you deal with; -->
</VirtualHost>
```

# how to

## setup site

```sh
sudo xed /etc/apache2/sites-available/manueldieterich.conf
sudo xed /etc/apache2/sites-available/radjajuschka.conf
```

```html
<VirtualHost *:80>
    ServerName manueldieterich
    DocumentRoot /home/frida/code/web/projects/sites/11ty/_global_builds/manueldieterich
</VirtualHost>
```

```html
<VirtualHost *:80>
    ServerName radjajuschka
    DocumentRoot /home/frida/code/web/projects/sites/11ty/_global_builds/radjajuschka
</VirtualHost>
```

```sh
sudo a2ensite manueldieterich # enable vhost
sudo a2ensite radjajuschka # enable vhost
sudo systemctl reload apache2
sudo apache2ctl restart # restart
```

```sh
sudo xed /etc/hosts # edit local dns resolution
```

```
127.0.0.1 manueldieterich
127.0.0.1 radjajuschka
```

## fs access

- Developers need read/write access to files so they can update the website
- Developers need read/write/execute on directories so they can browse around
- Apache needs read access to files and interpreted scripts
- Apache needs read/execute access to serveable directories
- Apache needs read/write/execute access to directories for uploaded content

```ini
# /etc/apache2/apache2.conf
User <user>
Group <group>
```

Sets the user ID as which the server will answer requests. In order to use this directive, **the server must be run initially as root**. If you start the server as a non-root user, it will fail to change to the lesser privileged user, and will instead continue to run as that original user. If you do start the server as root, then it is normal for the parent process to remain running as root; The user should have no privileges that result in it being able to access files that are not intended to be visible to the outside world, and similarly, the user should not be able to execute code that is not meant for HTTP requests. It is recommended that you set up a new user and group specifically for running the server. Some admins use user nobody, but this is not always desirable, since the nobody user can have other uses on the system.

#### apache binary and config

By default, permission for binary and configuration is 755 that means any user on a server can view the configuration.

```sh
# config file
stat /etc/apache2/apache2.conf # check permissions
sudo chmod 600 /etc/apache2/apache2.conf # owner: r,w

# [binary]
stat /usr/sbin/apache2 # check permissions
sudo chmod 700 /usr/sbin/apache2 # owner (root): r,w,x
```

#### run apache as extra user

```sh
# run Apache as separate user
sudo adduser --system --no-create-home apache # creates a user and group called apache
sudo addgroup apache
sudo adduser apache apache # adds user apache to group apache
sudo adduser frida apache # adds normal-user to group apache
```

#### fs permissions

document root

```sh
#### [user = group]
chown –R apache:apache /var/www/ # changes apache installation directory ownership to a newly created non-privileged user
```

```sh
#### [user vs. group]
# Vorteil: Der Server bekommt nur Schreibrechte auf ausgewählte Verzeichnisse/Dateien (über die Gruppe)

sudo chown -R admin:apache /var/www/ # 
sudo chown -R root:apache /srv/
sudo chmod -R 750 /var/www/ # r, x for the group (web server)
sudo chmod g+s /var/www/ # new files and folders (created by the web server) inherit group ownership from the parent folder (setuid/setgid)

# when the web server needs to write…
chmod g+w /var/www/<writable-folder> # run this command on each writable folder
```

```sh
#### [apache config]

sudo xed	/etc/apache2/envvars
# export APACHE_RUN_USER=apache
# export APACHE_RUN_GROUP=apache

sudo xed /etc/apache2/apache2.conf
```

```sh
 # make sure the *.key files are only readable by root
 # private key: 600 
 # public key: 644
 sudo chown -R root:root /etc/ssl/private/localhost.key
 sudo stat ./private/localhost.key
 stat certs/localhost.crt
```

```sh
ps -ef | grep http # greps for running http process and ensure it’s running with apache user; you should see one process is running with root. That’s because Apache is listening on port 80 and it has to be started with root
```

## disable cache

```ini
FileETag None
Header unset ETag
Header set Cache-Control "max-age=0, no-cache, no-store, must-revalidate"
Header set Pragma "no-cache"
Header set Expires "Wed, 21 Oct 2015 01:00:00 GMT"
```



## examples

`Require host example.org`  
`Require host .net example.edu`