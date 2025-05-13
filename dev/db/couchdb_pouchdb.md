# COUCHDB

Links
----------------

https://www.joshmorony.com/creating-a-multiple-user-app-with-pouchdb-couchdb/
https://nolanlawson.com/2013/11/15/couchdb-doesnt-want-to-be-your-database-it-wants-to-be-your-web-site/

## CLI

```sh
 snap logs couchdb -f
 sudo snap restart couchdb.server
```

## Paths

#### server

http://localhost:5984/
http://localhost:5984/_utils/

#### installation

`/snap/couchdb` # installation path 
`/var/snap/couchdb/` # data path

Config
--------------------

### paths


1. etc/default.ini
2. etc/default.d/*.ini
3. etc/local.ini
4. etc/local.d/*.ini

### snap

```sh
sudo xed /var/snap/couchdb/current/etc/local.ini
sudo xed /snap/couchdb/9/opt/couchdb/etc
```

### [couchdb]


```ini
[couchdb]
database_dir = /var/lib/couchdb
# Specifies location of CouchDB database files (*.couch named). This location should be writable and readable for the user the CouchDB service runs as (couchdb by default).
database_dir = ./data # default after snap install
view_index_dir = /var/lib/couchdb
default_security = admin_only 
default_security = admin_local
default_security = everyone
file_compression = snappy
max_dbs_open = 100 
# upper bound on the number of databases that can be open at once
max_document_size = 8000000 
# bytes
uri_file = /var/run/couchdb/couchdb.uri
# This file contains the full URI that can be used to access this instance of CouchDB. It is used to help discover the port CouchDB is running on (if it was set to 0 (e.g. automatically assigned any free one).
uuid = 0a959b9b8227188afc2ac26ccdf345a6
# Unique identifier for this CouchDB server instance.
```

### [couch_peruser]

```ini
[couch_peruser]
enable = true
# ensures that a private per-user database exists for each document in _users. These databases are writable only by the corresponding user. Databases are in the following form: userdb-{hex encoded username}
delete_dbs = true
# if a user is deleted, the respective database gets deleted as well
```

### [chttpd]

```ini
[chttpd]
port = 5984
port = 0
# let CouchDB use any free port
bind_address = 127.0.0.1
bind_address = 0.0.0.0
# let CouchDB listen any available IP address
authentication_handlers = {chttpd_auth, cookie_authentication_handler}
# used for Cookie auth
authentication_handlers = {chttpd_auth, default_authentication_handler}
# used for Basic auth
authentication_handlers = {chttpd_auth, jwt_authentication_handler}
# used for JWT auth
authentication_handlers = {chttpd_auth, proxy_authentication_handler}
# used for Proxy auth
config_whitelist = [{chttpd,config_whitelist}, {log,level}, {etc,etc}]
# Sets the configuration modification whitelist. Only whitelisted values may be changed via the config API.

enable_cors = false

require_valid_user_except_for_up = false

```

### [chttpd_auth]

```ini
[chttpd_auth]
allow_persistent_cookies = true
# CouchDB will set the Max-Age and Expires attributes on the cookie, which causes user agents to preserve the cookie over restarts.
require_valid_user = true
# no requests are allowed from anonymous users - everyone must be authenticated.
secret = 92de07df7e7a3fe14808cef90a7cc0d91
# secret token used for Proxy Authentication and for Cookie Authentication.
timeout = 600
# Number of seconds since the last request before sessions will be expired.

[couch_httpd_auth]
# Password must be 10 chars long and have one or more uppercase and
# lowercase char and one or more numbers.
password_regexp = [{".{10,}", "Min length is 10 chars."}, "[A-Z]+", "[a-z]+", "\\d+"]
```

### [cors]

```ini
[cors]
credentials = true
# CouchDB will respond to a credentials-enabled CORS request with an additional header:
# Access-Control-Allow-Credentials=true
origins = *
origins = http://localhost, https://localhost, http://couch.mydev.name:8080
headers = X-Couch-Id, X-Couch-Rev
# List of accepted headers
methods = GET,POST
# List of accepted methods
max_age = 3600
# Sets the Access-Control-Max-Age header in seconds. Use it to avoid repeated OPTIONS requests.
```

### [admins]

```ini
[admins]

```

```http
GET /_node/nonode@nohost/_config/admins HTTP/1.1
Accept: application/json
Host: localhost:5984
```

### [log]

```ini
[log]
writer = file
file = /var/log/couchdb/couch.log
writer = journald
level = info
level = notice
level = warn
level = err
level = crit
level = alert
level = emerg
```

###  [attachments

```ini
[attachments]
compression_level = 8
# from 1 (lowest, fastest) to 9 (highest, slowest). A value of 0 disables compression.
compressible_types = text/*, application/javascript, application/json, application/xml
# Since compression is ineffective for some types of files, it is possible to let CouchDB compress only some types of attachments, specified by their MIME type:
```



### config via HTTP API

Alternatively, configuration parameters can be set via the HTTP API. This API allows changing CouchDB configuration on-the-fly without requiring a server restart

Basics
------------------

> Because CouchDB is a web server, you can serve applications directly the browser without any middle tier.
> CouchDB doesn’t want to be your database; it wants to be your web site.

A *Couch app* is just a pure HTML/CSS/Javascript application, with only CouchDB as its backend, and this is the intended use case for CouchDB.

### users & authentication

By default, CouchDB has three user roles available:

* Server admin 		# super user
* Database admin	# can only read and write to a specific database
* Database reader 	# can only read documents from a specific database


How To
-------------------

1) How do we keep track of which todo belongs to which user?
2) How do we make sure that a user can only retrieve their own data?

* Create your own API using NodeJS
* Create a database for each user

```json
{
    "todos": {
        "329483424": {
            "t1": {
                "title": "bread"
            },
            "t2": {
                "title": "milk"
            }
        },
        "873298439": {
            "t1": {
                "title": "eggs"
            }
        },
        "984560992": {
            "t1": {
                "title": "cookies"
            }
        }
    }
}
```

### Basic Authentication


REST API
------------------

```sh
curl http://127.0.0.1:5984/
curl http://lem:test@127.0.0.1:5984 | jq # get database information
curl -X GET http://lem:test@127.0.0.1:5984/burkhard | jq # 

curl -H 'Content-Type: application/json' # specify the content type

curl -H 'Content-Type: application/json' -X POST --url http://lem:test@127.0.0.1:5984/burkhard -d '{"company": "Another"}'

```

# POUCHDB

### install

```sh
npm install --save pouchdb-browser
```



### init

```js
new PouchDB(name, options) // creates a database or opens an existing one. If you use a URL like 'http://domain.com/dbname', then PouchDB will work as a client to an online CouchDB instance. Otherwise it will create a local database using whatever backend is present.

let db = new PouchDB('http://localhost:5984/kittens') 
// The remote database will not be created until you do an API call, e.g.: db.info()

let dbuser = "lem"
let dbpw = "admin"
let db = new PouchDB(`http://${dbuser}:${dbpw}@127.0.0.1:5984/todo-lem`)
```

### info

```js
db.info().then(result => {
	console.log(result)
}).catch(err => {
	console.log(err);
})
```

### write

```js
/* Create a new document or update an existing document.
If the document already exists, you must specify its revision _rev, otherwise a conflict will occur.
If you want to update an existing document even if there’s conflict, you should specify the base revision _rev and use force=true option, then a new conflict revision will be created. */

/* create a new doc */

db.put({
  _id: 'mydoc',
  title: 'Heroes'
}).then(response => {
  // handle response
}).catch(err => {
  console.log(err);
})

/* db.post() */

db.post(doc, options) // Create a new document and let PouchDB auto-generate an _id for it

/* update an existing doc using _rev */

db.get('mydoc').then(doc => {
  return db.put({
    _id: 'mydoc',
    _rev: doc._rev,
    title: "Let's Dance"
  })
}).then(response => {
  // handle response
}).catch(err => {
  console.log(err)
})

```

#### response

The response contains the `id` of the document, the new `rev`, and an `ok` to reassure you that everything is okay.

```
{
  "ok": true,
  "id": "mydoc",
  "rev": "1-A6157A5EA545C99B00FF904EEF05FD9F"
}
```

#### put vs post

You should prefer `db.put()` to `db.post()`, because when you post(), you are missing an opportunity to use `allDocs()` to sort documents by _id (because your ids are random).

### read

#### get()

```js
db.get(docId, options, callback)
```



#### allDocs()

allDocs() returns the documents sorted by order of _id. This makes the _id a very powerful field that you can use for more than just uniquely identifying your documents. A way to take advantage of this is to use `new Date().toJSON()` as your document `_id`s. In this way, all your documents will be sorted by date.

```js
db.allDocs({
    include_docs: true,
    descending: true,
    
})
// include_docs: Include the document itself in each row in the doc field. Otherwise by default you only get the _id and _rev properties.
// descending: Reverse the order of the output documents.

```

### changes

listen to database changes

- an *event emitter* 
- emits a *change* event on each document change
- emits a *complete* event when all the changes have been processed
- emits an *error* event when an error occurs. 
- Calling cancel() will unsubscribe all event listeners automatically.

```js
db.changes({
	since: 'now', // 
    live: true, // emits change events for all future changes until cancelled
    include_docs: true // include the associated document with each change
}).on('change', showTodos);

/* example */

let changes = db.changes({
  	since: 'now',
  	live: true,
  	include_docs: true
}).on('change', change => {
  	// handle change
}).on('complete', info => {
  	// changes() was canceled
}).on('error', err => {
  	console.log(err);
});

changes.cancel(); // whenever you want to cancel
```

### sync & replication

```js
var sync = PouchDB.sync(src, target, options) // Sync data from src to target and target to src. 
// This is a convenience method for bidirectional data replication.

// this...
PouchDB.replicate('mydb', 'http://localhost:5984/mydb')
PouchDB.replicate('http://localhost:5984/mydb', 'mydb')
// is equivalent to...
PouchDB.sync('mydb', 'http://localhost:5984/mydb')

/* options */

let options = {
    live: true,
	retry: true

/* live: starts subscribing to future changes in the source database and continue replicating them. */
/* retry: will attempt to retry replications in the case of failure (due to being offline), using a backoff algorithm that retries at longer and longer intervals until a connection is re-established, with a maximum delay of 10 minutes. Only applicable if live: true. */
}

function sync() {
    syncDom.setAttribute('data-sync-state', 'syncing');
    var opts = {live: true};
    db.replicate.to(remoteCouch, opts, syncError);
	db.replicate.from(remoteCouch, opts, syncError);
}
```

#### replication events

```js
var sync = PouchDB.sync('mydb', 'http://localhost:5984/mydb', {
  live: true,
  retry: true
}).on('change', info => {
     /* fires when the replication has written a new document. 
     info will contain details about the change. 
     info.docs will contain the docs involved in that change. */
}).on('paused', err => {
      /* fires when the replication is paused, 
      either because a live replication is waiting for changes
      or replication has temporarily failed, with err, and is attempting to resume. */
}).on('active', () => {
      /* fires when the replication starts actively processing changes;
      e.g. when it recovers from an error or new changes are available. */
}).on('denied', err => {
  	/* fires if a document failed to replicate due to validation or authorization errors. */
}).on('complete', info => {
      /* fires when replication is completed or cancelled. 
      In a live replication, only cancelling the replication should trigger this event.
      info will contain details about the replication */
}).on('error', err => {
      /* fires when the replication is stopped due to an unrecoverable failure. 
      If retry is false, this will also fire when the user goes offline 
      or another network error occurs (so you can handle retries yourself, if you want).*/
})

sync.cancel(); // whenever you want to cancel
```

