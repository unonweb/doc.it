* a callback is passed as an argument to be called once the rest of that code has been run

#### 1. Keep your code shallow

You can start by moving the functions to the bottom of the file, then graduate to moving them into another file that you load in using a relative require like `require('./photo-helpers.js')` and then finally move them into a standalone module like `require('image-resize')`.

```js
var form = document.querySelector('form');

form.onsubmit = function (submitEvent) {
	var name = document.querySelector('input').value;
	request({
		uri: "http://example.com/upload",
		body: name,
		method: "POST"
	}, function (err, response, body) {
		var statusMessage = document.querySelector('.status')
		if (err) return statusMessage.value = err
		statusMessage.value = body
	})
}
```

* Name the two anonymous functions
* Move the functions to the top level

```js
document.querySelector('form').onsubmit = formSubmit;

function formSubmit (submitEvent) {
	var name = document.querySelector('input').value;
	request({
		uri: "http://example.com/upload",
		body: name,
		method: "POST"
	}, postResponse)
}

function postResponse (err, response, body) {
	var statusMessage = document.querySelector('.status');
	if (err) return statusMessage.value = err;
	statusMessage.value = body;
}
```

#### 2. Modularize

```js
// formuploader.js

module.exports.submit = formSubmit

function formSubmit (submitEvent) {
	var name = document.querySelector('input').value
	request({
		uri: "http://example.com/upload",
		body: name,
		method: "POST"
	}, postResponse)
}

function postResponse (err, response, body) {
	var statusMessage = document.querySelector('.status')
	if (err) return statusMessage.value = err
	statusMessage.value = body
}

// main.js

var formUploader = require('formuploader')
document.querySelector('form').onsubmit = formUploader.submit
```

* easier to understand -- you don't to read through all of the formuploader functions
* formuploader can get used in other places without duplicating code and can easily be shared on github or npm

#### 3. Handle every single error

* Error first callback: The most popular way to handle errors is the Node.js style where the first argument to the callback is always reserved for an error. 

```js
var fs = require('fs');

fs.readFile('/Does/not/exist', handleFile)

function handleFile(error, file) {
	if (error) return console.error('Uhoh, there was an error', error)
	// otherwise, continue on and use `file` in your code
}
```

### error-first callback


1. The first argument of the callback is reserved for an error object. If an error occurred, it will be returned by the first err argument
2. The second argument of the callback is reserved for any successful response data. If no error occurred, err will be set to null and any successful data will be returned in the second argument.

```js
fs.readFile('/foo.txt', (err, data) => {
	// If an error occurred, handle it (throw, propagate, etc)
	if(err) {
		console.log('Unknown Error');
		return;
	}
	// Otherwise, log the file contents
	console.log(data);
});
```
