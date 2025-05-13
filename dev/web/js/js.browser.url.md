
# URL

`const url = new URL(url, base)` # Creates and returns a URL object referencing the URL specified using an absolute URL string, or a relative URL string and a base URL string.
* *base* is the base URL to use in cases where url is relative

```js
const url = new URL('https://developer.mozilla.org:4097/en-US/docs/Web/API/URL/pathname?q=value');
url.host 		// developer.mozilla.org:4097
url.hostname 	// developer.mozilla.org
url.origin 		// https://developer.mozilla.org
url.href 		// 
url.pathname	// en-US/docs/Web/API/URL/pathname
url.protocol 	// https:
url.search 		// ?q=value

// parse a more complex URL:
let params = (new URL('https://example.com/?name=Jonathan%20Smith&age=18')).searchParams; // read only
let name = params.get('name'); // "Jonathan Smith".
let age = parseInt(params.get('age')); // 18


```
`host` # A USVString containing the domain (that is the hostname) followed by (if a port was specified) a ':' and the port of the URL.

# URLSearchParams

```js
let params = new URLSearchParams(document.location.search)
let name = params.get("name"); // "Jonathan"
let age = parseInt(params.get("age"), 10) // 18
```

