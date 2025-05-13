
The browser interface through which JavaScript can make HTTP requests. Since it is relatively new, it conveniently uses *promises* (which is rare for browser interfaces).

```js
const URL = 'https://copilot.events/franzk/api/graph'

async function getPictures() {
	let res = await fetch(URL, {
		method: 'POST',
		headers: {
			'Content-Type': 'application/json',
			'accept': 'application/json'
		},
	})

	if (res.ok) {
		let json = await res.json()
		console.dir(json)	
	}
	else {
		console.error('error: ', res.status, res.statusText)
	}
}

await getPictures()
```



```js
let responsePromise = fetch(requestObject)
let req = new Request('url') // creates a new Request object
let res = fetch('url', req)

// example GET
async function getPosts() {
  let res = await fetch('http://localhost:3000/api/posts')
  let json = await res.json()
  console.dir(json)
}

// example POST
let urlUnon = 'https://unonweb.de/api/post'
let data = 'my first post'
let reqOpt = {
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(data),
        method: "POST"
    }
let response = fetch(urlUnon, reqOpt)
```

* Returns a promise which is fulfilled once the response is available
* The promise resolves to a *Response* object
* requestObject has to be constructed using `let myRequest = new Request()`

## Request

```js
/* CONSTRUCTOR */
let req = new Request(url, {options}) // creates a new Request object
let req = new Request(
    'https://github.com/mdn/content/issues/12959', // url
  	{
        method: 'get',
        headers: { 
            'From': 'webmaster@example.org'
        },
        mode: 'cors', // no-cors, same-origin, navigate
		credentials: 'same-origin', // omit, same-origin, include
		redirect: 'follow' // error, manual
    }
)
```

```js
// A Request object is returned by...
FetchEvent.request
```

### methods & properties

```js
let req = new Request(url, {options})
req.body // A ReadableStream of the body contents
req.url // The URL of the request
req.method // The request's method (GET, POST, etc.)
```

### request.cache

* contains the cache mode of the request
* controls how the request will interact with the browser's HTTP cache

```js
fetch("some.json", { cache: "no-store" }).then((response) => {
  // The browser fetches the resource from the remote server without first looking in the cache, 
  // and will not update the cache with the downloaded resource.
  // Download a resource with cache busting, to bypass the cache completely
})

fetch("some.json", { cache: "reload" }).then((response) => {
  // The browser fetches the resource from the remote server without first looking in the cache, 
  // but then will update the cache with the downloaded resource.
});


fetch("some.json", { cache: "force-cache" }).then((response) => {
  // The browser looks for a matching request in its HTTP cache.
	// If there is a match, fresh or stale, it will be returned from the cache.
  // If there is no match, the browser will make a normal request, and will update the cache with the downloaded resource
});
```

## Response

```js
// A Response object is returned by...
FetchEvent.respondWith() // (Service Workers)
fetch()
let res = await fetch('url')
// or
fetch('url').then(res => { /* ... */ }
```

When we make a fetch request, the response will be given a response.type of "basic", "cors" or "opaque". These types indicate where the resource has come from and can be used to inform how you should treat the response object.

### methods & properties


```js
let res = await fetch('url')

/* status */
res.status // 200
res.ok // boolean, true if the HTTP status code is 200-299
```

```js
/* headers */

response.headers // object containing the headers
// show all headers:
for (let pair of res.headers.entries()) {
	console.log(`${pair[0]}:${pair[1]}`);
}
// get the value of a specific headers:
res.headers.get("Content-Type")); // image/jpeg
```

```js
/* body */

let bodyText = await res.text() // returns a promise that resolves with a string
let bodyJson = await res.json() // returns a Promise that resolves to a JavaScript object (not json!)
let bodyBlob = await res.blob()	// returns a promise that resolves with a Blob
let bodyArrayBuffer = await res.arrayBuffer() // returns a promise that resolves with an ArrayBuffer representation of the response body.

// convert body into TypedArray or Buffer
const bodyBuffer = Buffer.from(bodyArrayBuffer)
const bodyView = new Uint8Array(bodyArrayBuffer) // creates a typedArray view of the arrayBuffer
```

## FetchEvent

```js
/* methods */

fetchEvent.respondWith() 
// prevents the browser's default fetch handling, and allows you to provide a promise for a Response yourself

fetchEvent.waitUntil()
// Extends the lifetime of the event. Used to notify the browser of tasks that extend beyond the returning of a response, such as streaming and caching.

/* properties */

fetchEvent.request 
// returns the Request that triggered the event handler.
```

## Examples

```js
// EXAMPLE 1
// using the 'Content-Type' header as a condition for how the body is processed
let url = "http://localhost:5000/playgrnd/bird.png"
fetch(url)
	.then(response => {
		if (!response.ok) {
            throw new Error(`Failed to fetch: ${response}`)
        }
		let contentType = response.headers.get("Content-Type")
		if (contentType.includes('text')) return response.text()
		if (contentType.includes('image')) return response.blob()
		if (contentType.includes('json')) return response.json()
	})
	.then(data => {
		console.log(data)
	})

// EXAMPLE 2
// using try..catch
try {
  	await fetch('http://example.com')
} catch(err) {
    console.warn(`catched: ${err.stack}`)
}

// EXAMPLE 3
// using async and .then
async function fetchMoviesJSON() {
  const response = await fetch('/movies')
  const movies = await response.json()
  return movies
}
fetchMoviesJSON().then(movies => {
  movies // fetched movies
})

// EXAMPLE 4
// fetch an image and use it in the DOM
let myImage = document.querySelector('img')
let myRequest = new Request('flowers.jpg')

fetch(myRequest)
	.then(response => {
  		return response.blob()
	})
	.then(response => {
		var objectURL = URL.createObjectURL(response)
		myImage.src = objectURL
});

// EXAMPLE 7
let url = "http://localhost:5000/playgrnd/bird.png"
fetch(url)
    .then(response => {
        if (!response.ok) {
            throw new Error(`Failed to fetch: ${response}`)
        }
        for (let pair of response.headers.entries()) {
           console.log(`${pair[0]}:${pair[1]}`)
        }
        return response.blob()
    })
    .then(data => {
        console.log(data)
    })
    .catch(err => {
        console.warn(`catched: ${err.stack}`)
    })
```

## Troubleshooting

* When URL doesn’t start with a protocol name (such as *http:*), it is treated as relative (to the current document)

#### same-origin-policy

Es können nur HTTP-Ressourcen aus der gleichen Herkunft anfordern kann, aus der die Anwendung geladen wurde, es sei denn, die Antwort aus der anderen Herkunft enthält die richtigen CORS-Header.

#### Top-Level-Await

`await fetch("/playlists")` // await is only valid in async functions

Du hast zwei Möglichkeiten das Problem anzugehen:

* das gute alte `.then()` statt `await` benutzen, zumindest im Top-Level außerhalb von anderen asynchronen Funktionen
* den ganzen Code in einen Async-Wrapper verpacken:  
  `(async () => { /* dein Code */ })()`