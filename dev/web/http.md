# BROWSER

### implementation

A moderately complicated website can easily include anywhere from 10 to 200 resources.  
To be able to fetch those quickly, browsers will make several GET requests *simultaneously*, rather than waiting for the responses one at a time.

# VERSION

### differences

* HTTP/1.0 uses a new connection for each request/response exchange, where as HTTP/1.1 connection may be used for one or more request/response exchanges.

##### HTTP 1.0 and virtual hosting

If you are on a name-based virtual host (and each virtual server does not have its own separate IP address), then it is technically impossible to connect to your virtual host using HTTP/1.0; Only the default server --the first virtual server defined-- will be accessible. This is because HTTP/1.0 does not support the HTTP "Host" request header, and the Host header is required on name-based virtual hosts in order to "pick" which virtual host the request is being addressed to. In most cases, the response to a true HTTP/1.0 request will be a 400-Bad Request.


URI / URL
======================

### URI

```ini
[https]
# scheme://host
https://www.example.com
# scheme://host:port
https://www.example.com:8080
# scheme://host:port/path
https://www.example.com:8080/index.html
# scheme://host:port/path?query
https://www.example.com:8080/index.html?p1=A&p2=B
# scheme://host:port/path?query#fragment
https://www.example.com:8080/index.html?p1=A&p2=B#absatz3

# scheme://username:password@host:port
# deprecated for security reasons
https://max:muster@www.example.com:8080/index.html?p1=A&p2=B#absatz3
https://max:muster@www.example.com:8080/index.html?p1=A&p2=B#absatz3

[file]
# scheme://host/path
file://localhost/home/frida/code/web/projects/apps/todo_app/index.html
# scheme:///path
file:////home/frida/code/web/projects/apps/todo_app/index.html
# if host is omitted, it is taken to be "localhost"  
# when omitting "host", the slash is not omitted; therefore it's three slashes  

[vnc]
vnc://[host[:port]][?params]

[ws]
ws:hierarchicalpart
```


scheme	|user 	|pw 	|host 				|port 	|path 		|query 		|fragment
--------|-------|-------|-------------------|-------|-----------|-----------|-------------
https://|max:	|muster@|www.example.com:	|8080/	|index.html |?p1=A&p2=B |#absatz3


### URL

*URLs* sind eine Unterart der generellen Identifikationsbezeichnung mittels URI; da URLs die erste und häufigste Art von URIs darstellen, werden die Begriffe häufig synonym verwendet.

URL = `scheme:scheme-specific-part`

### query strings

```http
GET /example/message.html?name=Jean&message=Yes%3F HTTP/1.1
```

Composed by pairs of names and values, corresponding to the `name` attribute on the form field elements and the content of those elements, respectively. An ampersand character (`&`) is used to separate the pairs.

### URL encoding

Some characters in query strings must be escaped.  
URL encoding, uses a percent sign followed by two hexadecimal digits.  
? --> %3F

JavaScript provides the `encodeURIComponent` and `decodeURIComponent` functions to encode and decode this format.

```js
console.log(encodeURIComponent("Yes?")); // Yes%3F
console.log(decodeURIComponent("Yes%3F")); // Yes?
```

CLIENT REQUEST
================================

```http
GET /hello.html HTTP/1.1 	
Host: www.hello.com

# an empty line separates the request header from the request body
# request body
# logically the body is not used in GET requests but very much used in POST requests
```

```ini
GET / HTTP/1.1
# (slash) will usually fetch a /index.html file if there is one
GET https://www.epplehaus.de/
Host: www.example.com 
# the only mandatory request header
# value distinguishes between various DNS names sharing a single IP address, allowing name-based virtual hosting
```

methods
-------------------------------

#### GET

```http
GET /hello.html HTTP/1.1 	
Host: www.hello.com
```

Used to retrieve a resource from a web server. 

* sends parameters directly in the URL query string
* Requests using GET should only retrieve data and should have no other effect on the data.
* intended to be used to request an online resource
* the default method used by web browsers to make requests unless code instructed to do otherwise.
* any data sent to the server along with the request is present either in the URL query string, or in a cookie header
* a query string (name/value pairs) is sent in the URL of a GET request:

#### HEAD

```http
HEAD /hello.html HTTP/1.1 	
Host: www.hello.com
```

HEAD works exactly like GET, but the server *returns only the headers* in the response

#### POST

```http
POST /hello.html HTTP/1.1 	
Host: www.hello.com
```

Send data to the server to create/update a resource.  
The client uses the POST method to send data to the server. It's typically used in forms, for example, but also when interacting with a REST API.

* intended to be used to ask the server to save new data about an online resource
* any data included in the request is present either in the *body* of the request, the *query string*, or in a *cookie header*

#### html forms and http methods

```html
<!-- GET: the information in the form is added to the end of the action URL as a query string -->
<form method="GET" action="example/message.html">
	<p>Name: <input type="text" name="name"></p>
	<p>Message:<br><textarea name="message"></textarea></p>
	<p><button type="submit">Send</button></p>
</form>

<!-- POST: puts the query string in the body of the request -->
<form method="POST" action="example/message.html">
	<p>Name: <input type="text" name="name"></p>
	<p>Message:<br><textarea name="message"></textarea></p>
	<p><button type="submit">Send</button></p>
</form>
```

headers
-----------

```http
POST /hello.html HTTP/1.1
Host: www.hello.com
Accept-Charset: utf-8
Accept-Encoding: gzip, deflate
Accept-Language: en-US
Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==
Cache-Control: no-cache
Connection: keep-alive 
Connection: close
Content-Type: text/plain
Content-Type: text/html; charset=UTF-8
Content-Type: application/javascript;charset=utf-8
Content-Type: application/json
Content-Type: application/x-www-form-urlencoded
Content-Length: 348
Content-Encoding: gzip
Cookie: $Version=1; Skin=new;
If-Match: "737060cd8c284d8af7ad3082f209582d"
If-Modified-Since: Sat, 29 Oct 1994 19:43:31 GMT
If-None-Match: "737060cd8c284d8af7ad3082f209582d"
If-Range: "737060cd8c284d8af7ad3082f209582d"
If-Unmodified-Since: Sat, 29 Oct 1994 19:43:31 GMT
Referer: http://en.wikipedia.org/wiki/Main_Page
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:12.0) Gecko/20100101 Firefox/12.0
```

```ini
GET / HTTP/1.1
# (slash) will usually fetch a /index.html file if there is one
Host: www.example.com 
# distinguishes between various DNS names sharing a single IP address, allowing name-based virtual hosting
# If no port is included, the default port for the service requested is implied

[Accept]
Accept: text/html
# MIME-Types, die der Client akzeptiert
Accept-Charset:
# Zeichensätze, die der Client akzeptiert
Accept-Encoding:
# Komprimierungsformate, die der Client akzeptiert
Accept-Language:
# List of acceptable human languages for response

[Authorization]
Authorization:
# Anmeldedaten für geschützte Bereiche (Antwort auf `WWW-Authenticate`)

[Cache]
Cache-Control: no-cache
# Einstellungen für Caching der Ressourcen

[Content]
Content-Length: 
# The size of the resource, in decimal number of bytes. 
# The length of the request body in octets (8-bit bytes)
Content-Type: text/plain
Content-Type: text/html; charset=UTF-8
Content-Type: application/javascript;charset=utf-8
Content-Type: application/json
# the client tells the server what type of data is actually sent.
Content-Encoding: 
# Used to specify the compression algorithm.  

[Connection]
# Must not be used with HTTP/2
Connection: keep-alive 
# Verbindungen offen halten
Connection: close
# Verbindungen schließen

[Cookie]
Cookie:
# An HTTP cookie previously sent by the server with Set-Cookie.

[If]
If-Match: "737060cd8c284d8af7ad3082f209582d"
# Fordert eine Ressource unter der Bedingung an, dass das ETag einem bestimmten Wert entspricht
# Only perform the action if the client supplied entity matches the same entity on the server. This is mainly for methods like PUT to only update a resource if it has not been modified since the user last updated it.
If-Modified-Since: Sat, 29 Oct 1994 19:43:31 GMT
# Fordert die Ressource nur an, falls sie seit dem angegebenen Datum geändert wurde
If-None-Match:
# Fordert die Ressource an, falls das ETag nicht dem angegebenen Wert entspricht
If-Range:
# Fordert einen bestimmten Bereich unter einer Bedingung (ETag oder Änderungsdatum) an
If-Unmodified-Since:
# Fordert die Ressource an, falls sie seit dem angegeben Datum nicht geändert wurde

[CORS]
Origin: http://www.example-social-network.com
# Initiates a CORS request (asks server for Access-Control-* response fields)

Referer: http://en.wikipedia.org/wiki/Main_Page
# URL des Dokuments, das auf das aktuelle verwies (i.d.R. ein Hyperlink)
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:12.0) Gecko/20100101 Firefox/12.0
# Selbstidentifikation der Client-Software
```

# SERVER RESPONSE

1. status line (status code + human readable)
2. response header (the only mandatory one is *Host*)

```http
200 OK 						
Host: www.hello.com

<html>
  <head>
    <title>An Example Page</title>
  </head>
  <body>
    <p>Hello World, this is a very simple HTML document.</p>
  </body>
</html>
```

## status line

- `1XX` (informational)
  The request was received, continuing process.

- `2XX` (successful)
  The request was successfully received, understood, and accepted.

- `3XX` (redirection)
  Further action needs to be taken in order to complete the request.

- `4XX` (client error)
  The request contains bad syntax or cannot be fulfilled.

- `5XX` (server error)
  The server failed to fulfill an apparently valid request.

# MECHANISMS

## Content negotiation

### server-driven 

(proactive)

* based on user agent-provided acceptance criteria
* 'q' - quality - factor

```http
GET /hello.html HTTP/1.1
Host: www.hello.com
Accept: text/html; q=1.0, text/*; q=0.8, image/gif; q=0.6, image/jpeg; q=0.6, image/*; q=0.5, */*; q=0.1
Accept-Language: de; q=1.0, en; q=0.5
Accept-Charset:
Accept-Encoding:
```

* German is preferred but English is acceptable
* HTML (`text/html`) is preferred over other text types (`text/*`)
* GIF (`image/gif`) or JPEG (`image/jpg)` images are preferred over other image formats (`image/*`)
* any other media type (`*/*`) is accepted as a last resort: 

### Agent-driven

(reactive)
