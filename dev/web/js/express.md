# CONCEPTS

## router vs middleware

* Route functions *end* the HTTP request-response cycle by returning some response to the HTTP client
* Middleware functions typically perform some operation on the request or response and then call the next function in the "stack", which might be more middleware or a route handler.

**If the middleware does not end the cycle then it must call `next()` to pass control to the next middleware function (or the request will be left hanging).**

Middleware and routing functions are called in the order that they are declared.

It is almost always the case that *middleware is called before setting routes*, or your route handlers will not have access to functionality added by your middleware.

**The only difference between a middleware function and a route handler callback is that *middleware functions have a third argument* `next`, which middleware functions are expected to call if they are not that which completes the request cycle**

```js
// app.js
var usersRouter = require('./routes/users');
app.use('/users', usersRouter);

// ./routes/users.js
var router = express.Router();
router.get('/', (req, res, next) => {
    // the path specified here ('/') is appended to 
    // the path specified by app.use() ('/users')
    res.send('This is .../users');
});

module.exports = router;
```

## middleware

```js
// An example middleware function
function middlewareFctn(req, res, next) {
  // ... perform some operations
  next(); // Call next() so Express will call the next middleware function in the chain.
}
app.use(middlewareFctn); // added for all routes and verbs
app.use('/someroute', middlewareFctn); // added for a specific route
app.get('/', middlewareFctn); // added for a specific HTTP verb and route
```

```js
function requestTime(req, res, next) {
  req.requestTime = Date.now()
  next()
}

app.use(requestTime) // use requestTime an any request

app.get('/', (req, res) => {
  let responseText = 'Hello World!<br>'
  responseText += `<small>Requested at: ${req.requestTime}</small>`
  res.send(responseText)
})
```

## routers

```js
// mary.js - Mary route module
const express = require('/home/frida/npm-global/lib/node_modules/express');
const router = express.Router();
router.get('/', (req, res) => {
	res.send("Mary's home page");
});
module.exports = router;

// app.js
const mary = require('./mary.js');
app.use('/mary', mary);
```

To use the router in our main app file we would then `require()` the route module (wiki.js), then call `use()` on the Express application to add the Router to the middleware handling path. The two routes will then be accessible from /wiki/ and /wiki/about/.

## views

```js
const express = require('express');
const path = require('path');
const app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');

app.get('/', (req, res) => {
    res.render('index', { title: 'About dogs', message: 'Dogs rock!' });
    // Assuming that there's a template file named "index.njk" that contains placeholders for data variables named 'title' and "message"
});
```

## Route parameters

Route parameters are named URL segments that are used to capture the values specified at their position in the URL. The captured values are populated in the `req.params` object, with the name of the route parameter specified in the path as their respective keys.

```javascript
app.get('/users/:userId/books/:bookId', (req, res) => {
  res.send(req.params)
})
```

## static files

```js
app.use(express.static('public'))
app.use(express.static('media'))
app.use(express.static(path.join(__dirname, 'public')))
```

## Handling errors

Errors are handled by one or more *special middleware* functions that *have four arguments*, instead of the usual three:

```js
app.use(function(err, req, res, next) {
	console.error(err.stack);
	res.status(500).send('Something broke!');
});
```
These can return any content required, but must be called after all other app.use() and routes calls so that they are the last middleware in the request handling process!

## Using databases

The database itself can be installed locally or on a cloud server. In your Express code you require the driver, connect to the database, and then perform create, read, update, and delete (CRUD) operations

APP
=================

The `app` object conventionally denotes the Express application. Create it by calling the top-level `express()` function exported by the Express module

```js
const express = require('express')
const app = express()

app.get('/', function (req, res) {
  res.send('hello world')
})

app.listen(3000)
```


* when the callback is invoked the first argument will always be the *request* and the second will always be the *response*

```js
app.set(name, value) // Assigns setting name to value
// You may store any value that you want, but certain names can be used to configure the behavior of the server.

// for Boolean properties the following are equivalent: 
app.set('foo', true);
app.set('foo', false);
app.enable('foo');
app.disable('foo')

// Retrieve the value of a setting with app.get().
app.set('title', 'My Site')
app.get('title') // "My Site"
```

## app.get()

```js
app.get(path, callback) 
// Routes HTTP GET requests to the specified path with the specified callback functions.
app.get('/', (req, res) => {
	res.send('GET request to homepage');
}) 
```

## app.listen()

* identical to Node’s `http.Server.listen()`

```js
app.listen([port, host, backlog, callback) 
// Binds and listens for connections on the specified host and port. 
// Identical to Node’s http.Server.listen()
// If port is omitted or is 0, the operating system will assign an arbitrary unused port
```

* returns an `http.Server` object and (for HTTP) is a convenience method for the following:

```js
app.listen = function () {
  var server = http.createServer(this)
  return server.listen.apply(server, arguments)
}
```

The `app` returned by `express()` is in fact a JavaScript function, designed to be passed to Node’s HTTP servers as a callback to handle requests. This makes it easy to provide both HTTP and HTTPS versions of your app with the same code base, as the app does not inherit from these (it is simply a callback):

```javascript
var express = require('express')
var https = require('https')
var http = require('http')
var app = express()

http.createServer(app).listen(80)
https.createServer(options, app).listen(443)
```

## app.use()

```js
app.use(path, callback, callback...)
// Mounts the specified middleware function or functions at the specified path
// the middleware function is executed when the base of the requested path matches 'path'

app.use('/apple', ...) // will match “/apple”, “/apple/images”, “/apple/images/news”, and so on.
```

Since path defaults to “/”, middleware mounted without a path will be executed for every request to the app.

```js
// This middleware function will be executed for every request to the app:
app.use((req, res, next) => {
  console.log('Time: %d', Date.now())
  next()
})
```



## app.post()

```js
app.post(path, callback, callback...)
// Routes HTTP POST requests to the specified path with the specified callback functions.

app.post('/', function (req, res) {
	res.send('POST request to homepage')
})
```

## app.route()

You can create chainable route handlers for a route path by using `app.route()`. Because the path is specified at a single location, creating modular routes is helpful, as is reducing redundancy and typos. For more information about routes, see: [Router() documentation](https://expressjs.com/en/4x/api.html#router).

Here is an example of chained route handlers that are defined by using `app.route()`.

```javascript
app.route('/book')
  .get((req, res) => {
    res.send('Get a random book')
  })
  .post((req, res) => {
    res.send('Add a book')
  })
  .put((req, res) => {
    res.send('Update the book')
  })
```

### app.set(name, value)

Assigns setting `name` to `value`. You may store any value that you want, but certain names can be used to configure the behavior of the server. These special names are listed in the app settings table.

Calling `app.set('foo', true)` for a Boolean property is the same as calling `app.enable('foo')`.

# Router

`router.get` is only for defining subpaths. Consider this example:

```js
const router = express.Router();

app.use('/first', router); // Mount the router as middleware at path /first

router.get('/sud', smaller);

router.get('/user', bigger);
```

- If you open **/first/sud**, then the `smaller` function will get called.
- If you open **/first/user**, then the `bigger` function will get called.

In short, `app.use('/first', router)` mounts the middleware at path **/first**, then `router.get` sets the subpath accordingly.

# EXPRESS

```js
express.static(root, options)
```

## express.Router

Use the `express.Router` class to create modular, mountable route handlers. A `Router` instance is a complete middleware and routing system; for this reason, it is often referred to as a “mini-app”.

The following example creates a router as a module, loads a middleware function in it, defines some routes, and mounts the router module on a path in the main app.

Create a router file named `birds.js` in the app directory, with the following content:

```javascript
const express = require('express')
const router = express.Router()

// middleware that is specific to this router
router.use((req, res, next) => {
  console.log('Time: ', Date.now())
  next()
})
// define the home page route
router.get('/', (req, res) => {
  res.send('Birds home page')
})
// define the about route
router.get('/about', (req, res) => {
  res.send('About birds')
})

module.exports = router
```

Then, load the router module in the app:

```javascript
const birds = require('./birds')

// ...

app.use('/birds', birds)
```

The app will now be able to handle requests to `/birds` and `/birds/about`, as well as call the `timeLog` middleware function that is specific to the route.

REQ
========================

### Properties

```js
req.ip // the remote IP address of the request.
req.method // a string corresponding to the HTTP method of the request: GET, POST, PUT, and so on.
req.hostname // the hostname derived from the Host HTTP header.
req.path // the path part of the request URL.
req.secure // A Boolean property that is true if a TLS connection is established.
```

RES
===========================

### Properties

#### res.app

```js
res.app
// holds a reference to the instance of the Express application that is using the middleware.
```

### Methods

#### res.send()

```js
res.send(body) // Sends the HTTP response.
// body can be a Buffer object, a String, an object, Boolean, or an Array
res.send(Buffer.from('whoop'))
res.send({ some: 'json' })
res.send('<p>some html</p>')
res.status(404).send('Sorry, we cannot find that!')
res.status(500).send({ error: 'something blew up' })

// When the parameter is a String, the method sets the Content-Type to “text/html”:
res.send('<p>some html</p>')
// When body is an Array or Object, Express responds with the JSON representation:
res.send({ user: 'tobi' })
res.send([1, 2, 3])
```

#### res.sendFile()

Here is an example of using `res.sendFile` with all its arguments.

```javascript
app.get('/file/:name', function (req, res, next) {
  var options = {
    root: path.join(__dirname, 'public'),
    dotfiles: 'deny',
    headers: {
      'x-timestamp': Date.now(),
      'x-sent': true
    }
  }

  var fileName = req.params.name
  res.sendFile(fileName, options, function (err) {
    if (err) {
      next(err)
    } else {
      console.log('Sent:', fileName)
    }
  })
})
```

#### res.set()

```js
res.set(field [, value])
//Sets the response’s HTTP header field to value. To set multiple fields at once, pass an object as the parameter.

res.set('Content-Type', 'text/plain')
res.set({
    'Content-Type': 'text/plain',
    'Content-Length': '123',
    ETag: '12345'
})
```


#### res.status()

```js

res.status(code)
// Sets the HTTP status for the response.

res.status(403).end()
res.status(400).send('Bad Request')
res.status(404).sendFile('/absolute/path/to/404.png')
```

#### res.type()

```js
res.type(type)
// Sets the Content-Type HTTP header to the MIME type as determined by the specified type. 
// If type contains the “/” character, then it sets the Content-Type to the exact value of type, otherwise it is assumed to be a file extension and the MIME type is looked up in a mapping using the express.static.mime.lookup() method.

res.type('.html')
// => 'text/html'
res.type('html')
// => 'text/html'
res.type('json')
// => 'application/json'
res.type('application/json')
// => 'application/json'
res.type('png')
// => 'image/png'
```
