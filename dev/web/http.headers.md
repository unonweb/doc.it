## headers

```http
200 OK				
Access-Control-Allow-Origin: https://javascript.info
Access-Control-Expose-Headers: Content-Length,API-Key
Access-Control-Allow-Credentials:
Access-Control-Max-Age:
Access-Control-Allow-Methods:
Access-Control-Allow-Headers:
API-Key: 2c9de507f2c54aa1
Connection: close
Content-Type:text/html; charset=UTF-8
Content-Length: 12345
Content-Language: de
Content-Location: /index.htm
Date: Mon, 23 May 2005 22:38:34 GMT
ETag: "737060cd8c284d8af7ad3082f209582d"
Last-Modified: Wed, 08 Jan 2003 23:11:55 GMT
Location: http://www.w3.org/pub/WWW/People.html
Server: Apache/1.3.3.7 (Unix) (Red-Hat/Linux)
Set-Cookie: UserID=JohnDoe; Max-Age=3600; Version=1
WWW-Authenticate: Basic

<html>
  <head>
    <title>An Example Page</title>
  </head>
  <body>
    <p>Hello World, this is a very simple HTML document.</p>
  </body>
</html>
```



```ini
X-Content-Type-Options: nosniff
# prevents the browser from MIME sniffing

Accept-Language: en-US

Access-Control-Allow-Origin: https://javascript.info
Access-Control-Expose-Headers: Content-Length,API-Key
Access-Control-Allow-Credentials:
Access-Control-Max-Age:
Access-Control-Allow-Methods:
Access-Control-Allow-Headers:

Age: 12
# The age the object has been in a proxy cache in seconds
Allow: GET, HEAD
# Valid methods for a specified resource. To be used for a 405 Method not allowed

Content-Type: text/plain
Content-Type: text/html; charset=UTF-8
Content-Type: application/javascript;charset=utf-8
Content-Type: application/json
# the server tells the client what the content type of the returned content actually is. Browsers will do MIME sniffing in some cases and will not necessarily follow the value of this header; to prevent this behavior, the header `X-Content-Type-Options` can be set to `nosniff`.
Content-Length: 155 
# indicates its length in bytes
Content-Language: da
# Describes the human language(s) intended for the audience, so that it allows a user to differentiate according to the users' own preferred language.  
Content-Location: /index.htm
# Indicates an alternate location for the returned data.

ETag: "3f80f-1b6-3e1cb03b"
# eine aus diversen Meta-Informationen berechnete ID, z.B. zur Aktualitätsprüfung
# used to determine if a cached version of the requested resource is identical to the current version of the resource on the server
Last-Modified: Wed, 08 Jan 2003 23:11:55 GMT
# Gibt Datum und Uhrzeit der letzten Änderung an
Location: http://www.w3.org/pub/WWW/People.html
# geänderte URL, unter der die Ressource erreichbar ist (Weiterleitung)
Server: Apache/2.4.1 (Unix)
# Selbstidentifikation des Servers
Set-Cookie: UserID=JohnDoe; Max-Age=3600; Version=1
# Cookie, das der Browser im Namen des Servers speichern soll
WWW-Authenticate: Basic
# Indicates the authentication scheme that should be used to access the requested entity.
```
