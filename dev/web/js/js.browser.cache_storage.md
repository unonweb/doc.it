# CACHESTORAGE

Represents the storage for Cache objects.  
The interface provides a master directory of all the named caches that can be accessed by a ServiceWorker or other type of worker or window scope. You can access CacheStorage through the global `caches` property.

Use CacheStorage.open() to open a specific named Cache object and then call any of the Cache methods to maintain the Cache.

CacheStorage

1. get a Cache object using `caches.open(myCache)`
2. call any of the `Cache.methods`

```js
CacheStorage.open(myCache)
// returns a Promise that resolves to the 'Cache' object matching the myCache (obtain a Cache instance)
// access CacheStorage through the global 'caches' property
caches.open(myCache).then(cache => {
  // Do something with your cache
})
```

```js
Cache.match(request, options)
// Returns a Promise that resolves to the response associated with the first matching request in the Cache object.
cache.match(request, {options}).then(response => {
    // Do something with the response
})

Cache.add(request) 
// Takes a URL, retrieves it and adds the resulting response object to the given cache. This is functionally equivalent to calling fetch(), then using put() to add the results to the cache.
cache.add(request).then(() => {
  // request has been added to the cache
})

Cache.addAll(requests) 
// takes an array of URLs, retrieves them, and adds the resulting response objects to the given cache. The request objects created during retrieval become keys to the stored response operations
cache.addAll(requests[]).then(() => {
    // requests have been added to the cache
})
```

## open()

Returns a Promise that resolves to the Cache object matching the *cacheName*.  
You can access CacheStorage through the global `caches` property.  
If the specified Cache does not exist, a new cache is created with cacheName and a Promise that resolves to this new Cache object is returned.

```js
caches.open(cacheName).then(cache => {
  	// Do something with your cache
});
```

## match()

Checks if a given Request or url string is a key for a stored *Response*.   
Returns a Promise for a Response, or a Promise which resolves to undefined if no match is found.

```js
caches.match(request, options).then(response => {
  	// request: request you want to match; can be a Request object or a URL string.
    // Do something with the response
})

let options = {
    // defaults are set to false
	ignoreSearch: true, 
	// ignore the query string in the url.
    ignoreMethod: true, 
	// prevents matching operations from validating the Request http method 
    // (normally only GET and HEAD are allowed.)
	ignoreVary: true, 
	// tells the matching operation not to perform VARY header matching. 
    // In other words, if the URL matches you will get a match 
    // regardless of whether the Response object has a VARY header or not.
    cacheName: '' 
	// represents a specific cache to search within.
}
```
