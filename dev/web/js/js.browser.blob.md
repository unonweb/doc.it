
# BLOB

The Blob object represents a file-like object of immutable, raw data; 

* can be read as text or binary data
* can be converted into a ReadableStream

```js
let options = {
	type: "", // The MIME type of the data that will be stored into the blob.
}
var newBlob = new Blob(array, options);
newBlob.size 	// bytes
newBlob.type 	// a string containing the file's MIME type
newBlob.text() 	// returns a Promise that resolves with a string containing the contents of the blob, interpreted as UTF-8.
```

