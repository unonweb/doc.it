# install

## mongodb-server

```sh
sudo apt-get install gnupg curl
```

```sh
# server-7
curl -fsSL https://pgp.mongodb.com/server-7.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
   --dearmor

echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```

```sh
# server-8
curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg \
   --dearmor
   
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] https://repo.mongodb.org/apt/ubuntu noble/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
```

```sh
sudo apt update
sudo apt install mongodb-org
sudo systemctl start mongod
```

## mongodb compass

* https://downloads.mongodb.com/compass/mongodb-compass_1.44.5_amd64.deb

# notes

## ulimit Considerations

Most Unix-like operating systems limit the system resources that a process may use. These limits may negatively impact MongoDB operation, and should be adjusted. See UNIX ulimit Settings for the recommended settings for your platform.

## paths

```sh
# If installed via the package manager...
ll /var/lib/mongodb # data directory
ll /var/log/mongodb #  log directory
```

## file permissions

By default, MongoDB runs using the `mongodb` user account. 

If you change the user that runs the MongoDB process, you must also modify the permission to the data and log directories to give this user access to these directories.

# mongod

## config

```sh
sudo xed /etc/mongod.conf
sudo nano /etc/mongod.conf
```

### net

https://www.mongodb.com/docs/manual/reference/configuration-options/#net-options

```sh
sudo ufw allow from <ip> to any port 27017
```

```yaml
net:
  port: 27017
  bindIp: <mongodb_server_ip>
```

## logging

```sh
sudo tail -f /var/log/mongodb/mongod.log | jq
```

# syntax

## id

```sh
{ _id: ObjectId('63e272cabcfc7ff27e20653f') }
```



## operators

### comparison

| Name                                                         | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `$eq`                                                        | Matches values that are equal to a specified value.          |
| [`$gt`](https://www.mongodb.com/docs/manual/reference/operator/query/gt/#mongodb-query-op.-gt) | Matches values that are greater than a specified value.      |
| [`$gte`](https://www.mongodb.com/docs/manual/reference/operator/query/gte/#mongodb-query-op.-gte) | Matches values that are greater than or equal to a specified value. |
| [`$in`](https://www.mongodb.com/docs/manual/reference/operator/query/in/#mongodb-query-op.-in) | Matches any of the values specified in an array.             |
| [`$lt`](https://www.mongodb.com/docs/manual/reference/operator/query/lt/#mongodb-query-op.-lt) | Matches values that are less than a specified value.         |
| [`$lte`](https://www.mongodb.com/docs/manual/reference/operator/query/lte/#mongodb-query-op.-lte) | Matches values that are less than or equal to a specified value. |
| `$ne`                                                        | Matches all values that are not equal to a specified value.  |
| [`$nin`](https://www.mongodb.com/docs/manual/reference/operator/query/nin/#mongodb-query-op.-nin) | Matches none of the values specified in an array.            |

### logical

| Name                                                         | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`$and`](https://www.mongodb.com/docs/manual/reference/operator/query/and/#mongodb-query-op.-and) | Joins query clauses with a logical `AND` returns all documents that match the conditions of both clauses. |
| [`$not`](https://www.mongodb.com/docs/manual/reference/operator/query/not/#mongodb-query-op.-not) | Inverts the effect of a query expression and returns documents that do *not* match the query expression. |
| [`$nor`](https://www.mongodb.com/docs/manual/reference/operator/query/nor/#mongodb-query-op.-nor) | Joins query clauses with a logical `NOR` returns all documents that fail to match both clauses. |
| [`$or`](https://www.mongodb.com/docs/manual/reference/operator/query/or/#mongodb-query-op.-or) | Joins query clauses with a logical `OR` returns all documents that match the conditions of either clause. |

#### $or

```sh
db.inventory.find( { $nor: [
		{ site: { $eq: "63e26e0ec3b8d9894be7bd46" } },
		{ site: { $eq: "63dbc290781e1a853d4a5138" } }, 
		{ site: { $eq: "63be9660e73aa3c5ac269b9e" } },
]})

# 63e26e0ec3b8d9894be7bd46 manu
# 63dbc290781e1a853d4a5138 radja
# 63be9660e73aa3c5ac269b9e maarts
```

#### $nor

```sh
db.inventory.find( { $nor: [ 
	{ price: 1.99 }, 
	{ sale: true } 
]})

db.inventory.find( { $nor: [
		{ site: { $eq: "63e26e0ec3b8d9894be7bd46" } },
		{ site: { $eq: "63dbc290781e1a853d4a5138" } }, 
		{ site: { $eq: "63be9660e73aa3c5ac269b9e" } }, 
]})
```

### element

| Name                                                         | Description                                            |
| ------------------------------------------------------------ | ------------------------------------------------------ |
| [`$exists`](https://www.mongodb.com/docs/manual/reference/operator/query/exists/#mongodb-query-op.-exists) | Matches documents that have the specified field.       |
| [`$type`](https://www.mongodb.com/docs/manual/reference/operator/query/type/#mongodb-query-op.-type) | Selects documents if a field is of the specified type. |

```sh
{ field: { $exists: <boolean> } }

{ author: { $exists: true } }
```



### evaluation

| Name                                                         | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`$expr`](https://www.mongodb.com/docs/manual/reference/operator/query/expr/#mongodb-query-op.-expr) | Allows use of aggregation expressions within the query language. |
| [`$jsonSchema`](https://www.mongodb.com/docs/manual/reference/operator/query/jsonSchema/#mongodb-query-op.-jsonSchema) | Validate documents against the given JSON Schema.            |
| [`$mod`](https://www.mongodb.com/docs/manual/reference/operator/query/mod/#mongodb-query-op.-mod) | Performs a modulo operation on the value of a field and selects documents with a specified result. |
| [`$regex`](https://www.mongodb.com/docs/manual/reference/operator/query/regex/#mongodb-query-op.-regex) | Selects documents where values match a specified regular expression. |
| [`$text`](https://www.mongodb.com/docs/manual/reference/operator/query/text/#mongodb-query-op.-text) | Performs text search.                                        |
| [`$where`](https://www.mongodb.com/docs/manual/reference/operator/query/where/#mongodb-query-op.-where) | Matches documents that satisfy a JavaScript expression.      |

### array

| [`$all`](https://www.mongodb.com/docs/manual/reference/operator/query/all/#mongodb-query-op.-all) | Matches arrays that contain all elements specified in the query. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`$elemMatch`](https://www.mongodb.com/docs/manual/reference/operator/query/elemMatch/#mongodb-query-op.-elemMatch) | Selects documents if element in the array field matches all the specified [`$elemMatch`](https://www.mongodb.com/docs/manual/reference/operator/query/elemMatch/#mongodb-query-op.-elemMatch) conditions. |
| [`$size`](https://www.mongodb.com/docs/manual/reference/operator/query/size/#mongodb-query-op.-size) | Selects documents if the array field is a specified size.    |

## nested properties

```sh
{ 'version.site': { $eq: "64c774b724856550eb1d0534" } }
{ 'version.site': "64c774b724856550eb1d0534" } # the same
```

# mongodump

```sh
mongodump <options> <connection-string>

mongodump --ssl # connect to a mongod or mongos that has ssl enabled
mongodump --host=<hostname:port> # mongodb host to connect to
mongodump --archive=<file> # dump as an archive to the specified path
mongodump --out=<dir> # output directory
mongodump --gzip # compress the files in the output dump directory
mongodump --archive=test.20150715.gz --gzip # compress the archive file
```

```sh
mongodump --out="/home/payload/backup/db_231223" mongodb://localhost:27017"
mongodump --archive="/home/payload/backup/db_231223.archive" mongodb://localhost:27017
```

# mongorestore

The `mongorestore` program loads data from either a binary database dump created by `mongodump` or the standard input into a `mongod` or `mongos` instance.

As `mongorestore` restores from the `dump/` directory, it creates the database and collections as needed and logs its progress:

```sh
mongorestore <options> <connection-string> <directory or file to restore>
mongorestore --dir=<dir> # input directory
mongorestore --gzip # decompress gzipped input
mongorestore --drop # Before restoring the collections from the dumped backup, drops the collections from the target database. --drop does not drop collections that are not in the backup.
```

When the restore includes the `admin` database, `mongorestore` with `--drop` removes all user credentials and replaces them with the users defined in the dump file. Therefore, in systems with `authorization` enabled, `mongorestore` must be able to authenticate to an existing user *and* to a user defined in the dump file. If `mongorestore` can't authenticate to a user defined in the dump file, the restoration process will fail, leaving an empty database.

If a collection is dropped and recreated as part of the restore, the newly created collection has a different UUID unless `--drop` is used with `--preserveUUID`.

## restore from a directory

```sh
mongorestore dump/ # restore from a dump directory to a local mongod instance running on port 27017
# You can also restore a specific collection or collections from the `dump/` directory:
mongorestore --nsInclude=test.purchaseorders dump/
```

## restore from a archive file

```sh
mongorestore --archive=<file> # Restores from the specified archive file
mongorestore --archive=/home/payload/backup/db_231223.archive mongodb://localhost:27017
```

The use of `--db` and `--collection` options are deprecated when restoring from a directory or an archive file. Instead, to restore from an archive or a directory, see `--nsInclude` instead.

```sh
--nsInclude="test.myCollection"
--nsInclude="reporting.*"
--nsInclude="dept*.bar"
```

```sh
archive=/home/payload/backup/24-06-10_10:20:45/db.archive
host=212.227.171.69:27017
srcDB=payloadcms-dev
destDB=payloadcms-3

mongorestore --archive=${archive} --host=${host} nsInclude="${srcDB}.*" --nsFrom="${srcDB}.*" --nsTo="${destDB}.*" --drop
```



# mongosh

## config

```sh
sudo xed /etc/mongosh.conf
```

### .mongoshrc

On startup, mongosh checks your HOME directory for a JavaScript file named `.mongoshrc.js`. 
If this file is found, mongosh reads the content of .mongoshrc.js before displaying the prompt for the first time.

If you use mongosh to evaluate a JavaScript file or expression, either by using the `--eval` option on the command line or by specifying a .js file, mongosh reads .mongoshrc.js *after* the JavaScript has finished processing. 

You can prevent .mongoshrc.js from being loaded by using the `--norc` option.

## options

### connection

```sh
mongosh --host strato --port 27017 # works
mongosh mongodb://strato:27017/payloadcms-8 # works

mongosh --host # Specifies the name of the host machine where the mongod or mongos is running. If this is not specified, the MongoDB Shell attempts to connect to a MongoDB process running on the localhost.
mongosh --port # Specifies the port where the mongod or mongos instance is listening. If --port is not specified, the MongoDB Shell attempts to connect to port 27017.

192.168.0.5:9999/foo # Foo database on 192.168.0.5 machine on port 9999
mongosh mongodb://192.168.0.5:9999/ships # start mongosh using 'ships' database on specified connection string:
```

### other

```sh
mongosh --shell # Enables the shell interface. If you invoke the mongosh command and specify a JavaScript file as an argument, or use --eval to specify JavaScript on the command line, the --shell option provides the user with a shell prompt after the file finishes executing. The --shell flag is mutually exclusive with --json.

mongosh --nodb # Prevents the shell from connecting to any database instances.
mongosh --norc # Prevents the shell from sourcing and evaluating ~/.mongoshrc.js on startup.
```

```sh
mongosh mongodb://strato:27017/payloadcms-8 --eval "printjson(db.serverStatus())"
```

**Please note:** if you are using Mongo operators,  starting with a $ sign, you'll want to surround the eval argument in  single quotes to keep the shell from evaluating the operator as an  environment variable:

## commands

### database

```sh
db # display the database you are using; should return 'test', which is the default database
show dbs # list the databases available to the user
use <db> # switches to <db> or creates <db> if not existing

db.createCollection(name, options)
db.users.insertOne({ name: 'Jon Snow' }) # creates the collection users (if not existing)
db.getMongo() # mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+1.5.0
db.dropDatabase(); # deletes the db in use
```

## javascript

### exec from mongosh

```js
load( "scripts/connect-and-insert.js" )
load( "/data/db/scripts/connect-and-insert.js" )
```

### exec from cli

```js
// /home/frida/code/mongo/queryImages.js
db = connect('mongodb://strato:27017/payloadcms-8');
printjson( db.images.find( {} ) );
```

```sh
mongosh --nodb --file /home/frida/code/mongo/queryImages.js # 
```

```sh
# other methods
mongosh --nodb --shell ~/.mongo-do-auth.js
mongosh mongodb://strato:27017/payloadcms-8 /home/frida/code/mongo/demo.js
mongosh mongodb://strato:27017/payloadcms-8 < /home/frida/code/mongo/demo.js
```

### methods

### query

#### find()

```sh
db.users.find() 
# returns all users
db.movies.find( { title: "Titanic" } )
db.globals.find( { _id: ObjectId('630480afa55a7ea73134cce6') } )
```

```sh
db.inventory.find( { tags: ["red", "blank"] } ) 
# queries for all documents where 'tags' is an array with exactly two elements, "red" and "blank", in the specified order
db.inventory.find( { tags: "red" } ) 
# queries for all documents where 'tags' is an array that contains the string "red" as one of its elements
db.inventory.find( { site: { $not: { $eq: "63e26e0ec3b8d9894be7bd46" } } } )
```

```sh
### query a document ###

find({ 
	status: { $in: [ "A", "D" ]	} 
}) # get all docs where status equals either "A" or "D"

find({ 
	"size.h": { $lt: 15 }, 
	"size.uom": "in", 
	status: "D" 
}) 
# get all documents where 
# the nested field h is less than 15, 
# the nested field uom equals "in", 
# and the status field equals "D"

db.globals.find(
  {_id: ObjectId('637fcb1abbcd311cb0b40471')},
	{'presentations.titlePresentation': 'Moral in einem Konflikt im Kontext von Flüchtlingsunterkünften'}
) 
# return doc with specified id
# return only the fields that match the specified value
```

#### update

```sh
db.collection.updateOne(<filter>, <update>, <options>)
db.collection.updateMany(<filter>, <update>, <options>)
db.collection.replaceOne(<filter>, <update>, <options>)
```

### operators

#### $rename

```sh
db.globals.updateMany( { _id: ObjectId('630480afa55a7ea73134cce6') }, { $rename: { "nmae": "name" } } )
```

# clients

## js

### MongoClient

```js
const MongoClient = require("mongodb").MongoClient;
const url = "mongodb://localhost:27017/";

MongoClient.connect(url, function (error, db) {
    if (error) throw error;
    const dbo = db.db("mydb");
    const user = { name: "John", password: "helloworld" };
    const userImposter = { whatever: "doe", password: "okok" };

    //inserting user
    dbo.collection("users").insertOne(user, function (error, result) {
        if (error) throw error;
        console.log(result);
    });

    //inserting garbage user
    dbo.collection("users").insertOne(userImposter, function (error, result) {
        if (error) throw error;
        console.log(result);
    });
});
```
### Mongoose

Mongoose is an ODM (**Object Data Modeling**)  library for MongoDB. While you don’t need to use an Object Data Modeling (ODM) or Object Relational Mapping (ORM) tool to have a great  experience with MongoDB, some developers prefer them. Many Node.js  developers choose to work with Mongoose to help with data modeling,  schema enforcement, model validation, and general data manipulation. And Mongoose makes these tasks effortless.

By default, MongoDB has a flexible data model. This makes MongoDB  databases very easy to alter and update in the future. But a lot of  developers are accustomed to having rigid schemas.

Mongoose forces a semi-rigid schema from the beginning. With Mongoose, developers must define a Schema and Model.

```js
const mongoose = require("mongoose");
// Database Connection
mongoose.connect("mongodb://127.0.0.1:27017/mydb1", {
    useNewUrlParser: true,
    useCreateIndex: true,
    useUnifiedTopology: true,
});

//user model
const User = mongoose.model("User", {
    username: { type: String },
    password: { type: String },
});

//new user object
const newUser = new User({
    username: "john-doe",
    password: "helloworld",
    hello: "hello", // This field won't be inserted because it doesn't exist in our User model. Mongoose maps the data according to the User model to the database
});

//inserting/saving the document in collection
newUser.save(function (error, result) {
    if (error) {
        console.log(error);
    } else {
        console.log(result);
    }
});
```

### model & schema

Models are defined using the *Schema* interface. The Schema allows you to define the fields stored in each document along with their validation requirements and default values.

Schemas are then "compiled" into models using the `mongoose.model()` method.  
Once you have a model you can use it to find, create, update, and delete objects of the given type.

Each *model* maps to a *collection of documents* in the MongoDB database.  
The documents will contain the fields/schema types defined in the model Schema.

```js

var mongoose = require('mongoose');
var Schema = mongoose.Schema; // get Schema class
var SomeModelSchema = new Schema({
  aString: String,
  aDate: Date
});

// Compile model from schema
let SomeModel = mongoose.model('SomeModel', SomeModelSchema);
// alternatively:
let SomeModel = mongoose.model('SomeModel', {
    aString: String,
    aDate: Date
});
```

#### schema types and field options

```js
var schema = new Schema(
{
    name: String,
    binary: Buffer,
    living: Boolean,
    updated: { type: Date, default: Date.now() },
    age: { type: Number, min: 18, max: 65, required: true },
    mixed: Schema.Types.Mixed,
    _someId: Schema.Types.ObjectId,
    array: [],
    ofString: [String], // You can also have an array of each of the other types too.
    nested: { stuff: { type: String, lowercase: true, trim: true } }
})
```

The code also shows both ways of declaring a field:

* Field *name* and *type* as a key-value pair 
    (i.e. as done with fields name, binary and living)
* Field *name* followed by an object defining the type, and any other options for the field. 
    Options include things like:
    * default values
    * built-in validators (max/min values) and custom validation functions
    * Whether the field is required
    * Whether *String* fields should automatically be set to lowercase, uppercase, or trimmed 
    (`{ type: String, lowercase: true, trim: true }`)

#### getting started

```js
//Import the mongoose module
var mongoose = require('mongoose');

//Set up default mongoose connection
var mongoDB = 'mongodb://127.0.0.1/my_database';
mongoose.connect(mongoDB, {useNewUrlParser: true, useUnifiedTopology: true});

//Get the default connection
var db = mongoose.connection;

//Bind connection to error event (to get notification of connection errors)
db.on('error', console.error.bind(console, 'MongoDB connection error:'));
```

