# Browser

## Same-Origin-Policy

The browser checks if the *origins* of the web application (http://localhost:3000) and the server match.

- don't talk to strangers
- **no one can request any data or perform any action outside his origin**
- origin: *https://www.example.com:8080* (protocol + domain + port)

## Cross-origin requests

Eine Webapp/Website stellt einen cross-origin request, wenn sie eine Ressource anfordert, die einen anderen Ursprung (Domain + Protokoll + Port) hat, als ihren eigenen – **requires special headers from the remote side**

* XMLHttpRequest
* Fetch

Eine Webapp/Website, die diese APIs verwendet, kann nur HTTP-Ressourcen aus der gleichen Herkunft (*Cross-Origin Request Blocked*) anfordern, aus der sie geladen wurde - es sei denn, die Antwort aus der anderen Herkunft enthält die richtigen CORS-Header.

**So wird verhindert, dass Javascript beim Client beliebigen Code oder beliebige Inhalte von überallher nachladen kann.**

There are two types of cross-origin requests:

* safe / simple requests
* all the others

CORS
----------------------------------------------

*Cross-Origin Resource Sharing*
Mechanismus, der Webbrowsern oder anderen Webclients Cross-Origin-Requests ermöglicht

* the need to talk to strangers
* CORS gives us options to relax SOP
* CLI clients don't obey the SOP

A typical use case is to have a static website hosted on a CDN make requests to another resource, such as a hosted CouchDB instance.

```sh
Access-Control-Allow-Origin: http://localhost:3000 # specify which origins can access the server’s resources
Access-Control-Allow-Origin: *
```



## CORS for safe requests

### mechanism

1. The client makes a cross-origin request. In doing so the browser automatically includes a special header

   ```http
   GET https://www.dkb.de/login HTTP/1.1
   Host: www.dkb.de
   Origin: https://malware.org
   ```

2. The server responses and adds a special header to that response:

   ```http
   200 OK
   Access-Control-Allow-Origin: https://malware.org
   ```

3. The client's browser compares the URL of the origin of the request with the URLs included in the response header. 
   If they match, the client's authorized to receive the response body 

The browser plays the role of a trusted mediator here:

* It ensures that the correct `Origin` is sent with a cross-origin request.
* It checks for permitting `Access-Control-Allow-Origin` in the response, if it exists, then JavaScript is allowed to access the response, otherwise it * s with an error.

## safe requests

* don't trigger a *CORS preflight*. 

A “simple request” is one that meets all the following conditions:

* Safe method: `GET`, `POST` or `HEAD`

Any other request is considered “unsafe”.

### safe request headers

```http
GET /hello.html HTTP/1.1 
Origin: https://developer.mozilla.org
Accept: text/html
Accept-Language:
Content-Language:
Content-Type: application/x-www-form-urlencoded
Content-Type: multipart/form-data
Content-Type: text/plain 
```

### response

#### safe response headers

(that JavaScript may access by default):

```http
200 OK
Access-Control-Allow-Origin: https://developer.mozilla.org
Content-Type:text/html; charset=UTF-8
Cache-Control:
Content-Language:
Content-Type:
Expires:
Last-Modified:
Pragma:
```

Accessing any other response header causes an error.  
To grant JavaScript access to any other response header, the server must send the `Access-Control-Expose-Headers` header. It contains a comma-separated list of unsafe header names that should be made accessible.

```ini
Access-Control-Allow-Origin: *
# wildcard same-origin policy
# means that any origin may make requests to this ressource
# appropriate when a page or API response is considered completely public content and it is intended to be accessible to everyone, including any code on any site
# widely and appropriately used in the object-capability model, where pages have unguessable URLs and are meant to be accessible to anyone who knows the secret. 
```

### Access-Control-Expose-Headers

```http
200 OK
Access-Control-Allow-Origin: https://developer.mozilla.org
Access-Control-Expose-Headers: Content-Length,API-Key
Content-Type: text/html; charset=UTF-8
Cache-Control:
Content-Language: de
Expires:
Last-Modified:
API-Key: 2c9de507f2c54aa1
Content-Length: 12345
```

* without `Access-Control-Expose-Headers: Content-Length,API-Key` the last two header fields could not be read by the client

## CORS for unsafe requests

If it's not a simple request the browser first sends an HTTP request using the *OPTIONS* method to the resource on the other origin, in order to determine if the actual request is safe to send.

1. The client wants to make a cross-origin request that is not a simple request

   ```js
   xhr.setRequestHeader('X-PINGOTHER', 'pingpong');
   xhr.setRequestHeader('Content-Type', 'application/xml');
   ```

   This is not a simple request since it uses a Content-Type of application/xml and a custom header.

2. The browser makes a preflight request in order to announce to the server what is going to happen:

   ```http
   OPTIONS /doc HTTP/1.1
   Origin: http://foo.example
   Access-Control-Request-Method: POST
   Access-Control-Request-Headers: X-PINGOTHER, Content-Type
   ```

   `Access-Control-Request-Headers`   # lets the server know which headers will be used when the actual request is made
   `Access-Control-Request-Method` 	# lets the server know which method will be used when the actual request is made.

3. The server checks the preflight request and answers:

   ```http
   200 OK
   Access-Control-Allow-Origin: http://foo.example
   Access-Control-Allow-Methods: POST, GET, OPTIONS
   Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
   Access-Control-Max-Age: 86400
   ```

   `Access-Control-Allow-Methods`
   specifies the methods allowed when accessing the resource in response to a preflight request  
   `Access-Control-Allow-Headers`
   used in response to a preflight request to indicate which HTTP headers can be used when making the actual request.

# HTTP

## CORS

1. The client makes a cross-origin request. In doing so the browser automatically includes a special header

   ```http
   GET /hello.html HTTP/1.1
   Host: www.hello.com
   Origin: https://developer.mozilla.org
   ```

2. The server responses and adds a special header to that response:

   ```http
   200 OK
   Access-Control-Allow-Origin: https://developer.mozilla.org
   ```

3. The client's browser compares the URL of the origin of the request with the URLs included in the response header. 
   If they match, the client's authorized to receive the response body 

### client

```http
GET /hello.html HTTP/1.1 
Origin: https://developer.mozilla.org
Accept: text/html
Accept-Language:
Content-Language:
Content-Type: application/x-www-form-urlencoded
Content-Type: multipart/form-data
Content-Type: text/plain 
```

### server

```http
200 OK
Access-Control-Allow-Origin: https://developer.mozilla.org
Access-Control-Expose-Headers: Content-Length,API-Key
Content-Type:text/html; charset=UTF-8
Cache-Control:
Content-Language:
Content-Type:
Expires:
Last-Modified:
API-Key: 2c9de507f2c54aa1
Content-Length: 12345
```

* without `Access-Control-Expose-Headers: Content-Length,API-Key` the last two header fields could not be read by the client