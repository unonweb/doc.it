# CLI

## node

https://nodejs.org/docs/latest-v20.x/api/cli.html#command-line-api

```sh
node --inspect-port=[host:]port # Set the host:port to be used when the inspector is activated. Useful when activating the inspector by sending the SIGUSR1 signal. Default host is 127.0.0.1

node --inspect=127.0.0.1:9229
node --inspect=127.0.0.1:2000 -r ts-node/register server.ts # if using ts-node
```

## nvm

### install

```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

### global modules

with active nvm node global modules are installed under specific version of node in `/home/frida/.nvm/versions/node/${version}`
nvm manages your global installs - separately, global installs should never be shared across node versions

## npx

Currently, NPX is bundled with NPM when you install the NPM version 5.2.0 or higher.

- NPX allows you to quickly run and test Node packages without installing them globally or locally, by caching the packages *temporarily* on your machine. 
- NPX automatically installs missing npm packages/dependencies for you. 
- NPX automatically installs the latest version of packages in a project when you run the `npx package` command.
- NPX allows you to run different versions of the same package, on  different projects. For example, if one project only works with Node v  11.1, and another only works with Node v 11.5, NPX makes that process painless.

### npx vs npm exec

When run via the `npx` binary, all flags and options *must* be set prior to any positional arguments.  When run via `npm exec`, a double-hyphen `--` flag can be used to suppress npm's parsing of switches and options that should be sent to the executed command.

```sh
npx foo@latest bar --package=@npmcli/foo
# In this case, npm will resolve the foo package name, and run the following command:
foo bar --package=@npmcli/foo
# Since the --package option comes after the positional arguments, it is treated as an argument to the executed command.

npx --node-arg=--inspect
```

### cli

```sh
nvm use 8.0 # use the latest available 8.0.x release
nvm use --lts # use the latest LTS version
nvm use node # use the latest version
nvm use system # go back to using the system’s version

# aliases #
nvm alias default 14 # change the default version

nvm run 6.10.3 app.js # run app.js using node 6.10.3

nvm current # current version in use
node -v # the same

nvm which current # /home/frida/.nvm/versions/node/v18.3.0/bin/node

nvm ls # all installed versions of Node
nvm ls-remote # list all available Node versions

# install #
# When you install a new version of Node, NVM immediately begins using it and designates it as the current version.
nvm install node # install the latest available version
nvm install --lts # install the latest LTS version

nvm install node --reinstall-packages-from default #  install a new version of Node with the packages from an earlier release
# reinstalling packages does not update the NPM version

# uninstall #
nvm uninstall 13.10.1
npm uninstall -g module
```

# MODULES: basics

A file extension must be provided when using the `import` keyword to resolve **relative or absolute** specifiers.

## import global modules

after global installing package, you have two ways:

- OR use full path in code: `require('/path/to/global/node_modules/global-package-name.js')`
- OR exec `npm link global-package-name` - this command creates symlink in local ***node_modules\*** directory

```sh
npm config get prefix # /home/frida/.node_modules
ll /home/frida/.node_modules/lib/node_modules/<module> # look for index.mjs or <name>.mjs
```

```js
import postcss from '/home/frida/.node_modules/lib/node_modules/postcss/lib/postcss.mjs'
import postcssPresetEnv from '/home/frida/.node_modules/lib/node_modules/postcss-preset-env/dist/index.mjs'
```

## require vs import

```js
export const A = 'A'
export default A

// This is equivalent to the CommonJS:

const A = 'A'
module.exports = {
  A,
  default: A,
}
```

### import mjs into cjs

**CommonJS modules can't require(ES Modules)**. 

* You are not able to import `.mjs` files from `.js` files. This is due to the different nature of the two systems.
* Exception (in Node.js) by using the `import()` function (which is a bit painful and not a great solution).

## es modules

### import cjs into mjs

**ES Modules can import CommonJS modules**

- ESM spec requires that import paths be valid paths, meaning you need the file extension and everything (CommonJS doesn't require that). Node has an option [to skip out on that requirement](https://nodejs.org/docs/latest-v16.x/api/esm.html#customizing-esm-specifier-resolution-algorithm) for ES modules, if you want to keep it old-school: `node --es-module-specifier-resolution=node your-dope-module.mjs`

#### ex1

```js
// book.cjs
module.exports = class Book {
    constructor(name) {
        this.name = name;
    }
}
```

```js
// student.js
export class Student {
    constructor(name) {
        this.name = name;
    }
}
```

```js
// app.js
import { Student } from "./student.js";
import Book from './book.cjs'; // use default import for commonjs named export

const student = new Student('Name');
const book = new Book('Name');
```

#### ex2

```js
// cjs.cjs
const name = "Darth Vader";
const ability = () => "Can drive spaceship";

module.exports = { name, ability };
```

```js
// esm.mjs
const name = "Messi";
const ability = () => "Can kick balls";

export default { name, ability };
export const extra = "He won the world cup";
```

```js
// importing esm in esm
import messi from "./esm.mjs";
console.log(messi.name, messi.ability());

// importing cjs in esm
import darthVader from "./cjs.cjs";
console.log(darthVader.name, darthVader.ability());

// We can also destructure the imported object from CommonJS just like we destructure an import from ESM.
import { name, ability } from "./cjs.cjs";
```

### createRequire()

```js
import { createRequire } from 'node:module';
const require = createRequire(import.meta.url);

// sibling-module.js is a CommonJS module.
const siblingModule = require('./sibling-module'); 
```

##  file extension

### package.json

```json
// Defines how Node.js should interpret .js files
"type": "module" // Tells Node.js to interpret .js files within that package as using ES module syntax.
// If a package.json file does not have a "type" field, .js files are treated as CommonJS.
```

By default, files with the `.js` extension will be treated as CommonJS modules, while files with the `.mjs` extension are treated as ES Modules.

* Files ending with `.mjs` are always loaded as **ES modules** regardless of the nearest parent package.json.
* Files ending with `.cjs` are always loaded as **CommonJS** regardless of the nearest parent package.json.

The .mjs and .cjs extensions can be used to **mix types within the same package**:

* Within a `"type": "module"` package, Node.js can be instructed to interpret a particular file as CommonJS by naming it with a .cjs extension (since both .js and .mjs files are treated as ES modules within a "module" package).
* Within a `"type": "commonjs"` package, Node.js can be instructed to interpret a particular file as an ES module by naming it with an .mjs extension (since both .js and .cjs files are treated as CommonJS within a "commonjs" package).The .mjs and .cjs extensions can be used to mix types within the same package:

You can also save a file with the **.mjs** extension to be able to use **import/export**, for example:

```js
// abc.mjs
const abc = () => console.log('hello')
export default abc;

// index.js
import abc from './abc.mjs';
abc()
```

## import()

import() is supported in both CommonJS and ES modules. In CommonJS modules it can be used to load ES modules.

```js
// export
export function hi() {
  alert(`Hello`)
}
export function bye() {
  alert(`Bye`)
}

// import
let {hi, bye} = await import('./say.js')
hi()
bye()

// default export
export default function() {
  alert("Module loaded (export default)!")
}

// default import
let obj = await import('./say.js')
let say = obj.default
// or, in one line: 
let { default: say } = await import('./say.js')
say()
```



### restricction

When using `import()` we have no choice but for the loaded module object to land inside a function. The module object cannot land directly in a global scope variable in a CommonJS module, because of  the inability to use `await` in the global scope. Either the module object lands in the `.then` of a Promise chain, or it is a result obtained via the `await` keyword inside an async function.

### cache

require.cache is not used by import. It has a separate cache.

> the es module cache is designed to be immutable. we are working on hooks to allow mocking and such but raw access to the cache will never be available.

## commonJS

### exports

#### default

```js
module.exports = function() {
  return "yup simple as that";
};
```

#### named export

```js
module.exports = function dateConvertToLocal() {}
module.exports.dateConvertToLocal = function() => {} // the same 
// or
function dateConvertToLocal() {}
module.exports = {
  dateConvertToLocal
}
```

#### named import

```js
const { dateConvertToLocal } = require('/home/payload/cms/src/html_templates/dateConvertToLocal.js')
```

### require

Basic functionality:
* it reads a JavaScript file
* executes the file
* return the exports object

To be loaded by the Node.js `require()` function, a module must be one of the following:

* A folder with a package.json file containing a "main" field.
* A folder with an index.js file in it.
* A JavaScript file.
* A JavaScript file is a module when it exports one or more of the symbols it defines, being them variables, functions, objects

```js
const package = require('<path>')
// If *path* refers to a directory, Node will try to load the file named *index.js* in that directory.
// If no relative or absolute path is given, is assumed to refer to either a built-in module or a module installed in a *node_modules* directory

const fs = require("fs") // loads Node’s built-in file system module
const robot = require("robot") // try to load the library found in *node_modules/robot/*
```

#### curly braces

```js
const { property } = require('object') // the same as the Python construct `from <module> import ...`
```


#### reading a JSON file with require()

```js
const myJson = require('./myJson.json');
// if myJson is an array of objects:
myJson.forEach(item => {
    console.log(`${item.name}: ${item.type}`)
})
```

Downsides of using the this method:
* It only reads the file once and cache data; requiring it again with simply return the cached data.
* The file must have the .json extension. Without the proper extension, the require() method won't treat it as a JSON file.
Because of the above limitations, require() is only suitable for loading static configuration files that don't change often. For reading a dynamic file like databases.json, you should use the fs.readFile() method instead.

#### examples


```js
// export in one file:
exports.uppercase = (str) => str.toUpperCase()
// require in another:
const uppercaseModule = require('uppercase.js')
uppercaseModule.uppercase('test')
```

```js 
exports.message = "hi";
exports.say = function () {
    console.log(exports.message);
}
```

### cache

when requiring a module, node caches the result

#### invalidate cache

Yes, you can access the cache via `require.cache[moduleName]` where `moduleName` is the name of the module you wish to access. Deleting an entry by calling `delete require.cache[moduleName]` will cause `require` to load the actual file.

```js
delete require.cache[path.join(__dirname, ‘../path/to/module.js’)]
require('../path/to/module.js')

function requireUncached(module) {
    delete require.cache[require.resolve(module)];
    return require(module);
}

requireUncached('./myModule') // instead of require()
```

MODULES: external
=============

## dotenv

Dotenv is a zero-dependency module that **loads environment variables from a .env file into process.env**

* website: https://github.com/motdotla/dotenv

```sh
npm install dotenv --save # local
```

1. create a .env file in the root of your project:

```ini
S3_BUCKET="YOURS3BUCKET"
SECRET_KEY="YOURSECRETKEYGOESHERE"
```

2. As early as possible in your application, import and configure dotenv:

```js
// require
require('dotenv').config()
console.log(process.env) // remove this after you've confirmed it is working
// es6
import * as dotenv from 'dotenv' 
dotenv.config()
```

## qs

```js
import qs from "qs";

const query = {
	and: [
		{
			site: { equals: '1501' } // all pages associated with the same site
		},
		{
			isHome: { equals: true }
		}
	]
}

const str = qs.stringify(query);
console.log(str)
```



## glob

```js
const glob = require('glob')
const root = resolve(__dirname, '_site')
let globOpts = {
    cwd: root
}

glob('de/*/*.html', globOpts, (err, files) => {
    if (err) console.log(err)
    else console.log(files) // an array of paths of the matching files
})

glob('*(de|en)/*/*.html', globOpts, (err, files) => {
    if (err) console.log(err)
    else console.log(files) // finds nested html files in both de/ and en/ directories
    }
})
```

## HTTP clients

### node-fetch

provides the promise-based fetch interface that we know from the browser

### axios

* performs automatic transformations of JSON data

```js
const axios = require('axios')

axios({
    method: 'get',
    url: 'http://bit.ly/2mTM3nY',
})

axios.get(url, {options})

// GET request
axios.get('https://example.com/todos')
  .then(res => {
    console.log(`statusCode: ${res.status}`)
    console.log(res)
  })
  .catch(error => {
    console.error(error)
})

// GET request for remote image in node.js
async function axiosImg(url, localPath) {
    let res = await axios.get(url, { responseType: 'stream' })
    res.data.pipe(fs.createWriteStream('ada_lovelace.jpg'))
    return new Promise((resolve, reject) => {
        writer.on('finish', resolve)
        writer.on('error', reject)
    })
}

// POST request
axios.post('https://whatever.com/todos', {
    todo: 'Buy the milk',
  })
  .then(res => {
    console.log(`statusCode: ${res.status}`)
    console.log(res)
  })
  .catch(error => {
    console.error(error)
  })

// other
axios.get('http://pokeapi.co/api/v2/pokemon/')
  .then((response) => {
    return response;
  })
  .catch((error) => {
    console.log(error)
  })
}

axios.post('http://pokeapi.co/api/v2/pokemon/', { pokemon: 'Pikachu',})
  .then((response) => {
  	return response;
  })
  .catch((error) => {
    console.log(error)
})
```

#### full examples

```js
const fs = require('fs')
const axios = require('axios')

async function axiosImgWithToken(url, localPath) {
	try {
		let token = await fetchToken('udo.nonner@posteo.de', '123abcAB')
		const writer = fs.createWriteStream(localPath)
		const res = await axios.get(url, {
			headers: {
				Authorization: `JWT ${token}`
			},
			responseType: 'stream'
		})
		res.data.pipe(writer)
		return new Promise((resolve, reject) => {
			writer.on('finish', resolve)
			writer.on('error', reject)
		})
	} catch (err) {
		console.error(err)
	}
}
```



#### request

```js
{
  // `url` is the server URL that will be used for the request
  url: '/user',

  // `method` is the request method to be used when making the request
  method: 'get', // default

  // `baseURL` will be prepended to `url` unless `url` is absolute.
  // It can be convenient to set `baseURL` for an instance of axios to pass relative URLs
  // to methods of that instance.
  baseURL: 'https://some-domain.com/api',

  // `headers` are custom headers to be sent
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // `params` are the URL parameters to be sent with the request
  // Must be a plain object or a URLSearchParams object
  // NOTE: params that are null or undefined are not rendered in the URL.
  params: {
    ID: 12345
  },

  // `paramsSerializer` is an optional function in charge of serializing `params`
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer: function (params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // `data` is the data to be sent as the request body
  // Only applicable for request methods 'PUT', 'POST', 'DELETE', and 'PATCH'
  // When no `transformRequest` is set, must be of one of the following types:
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - Browser only: FormData, File, Blob
  // - Node only: Stream, Buffer
  data: {
    firstName: 'Fred'
  },
  
  // syntax alternative to send data into the body
  // method post
  // only the value is sent, not the key
  data: 'Country=Brasil&City=Belo Horizonte',

  // `timeout` specifies the number of milliseconds before the request times out.
  // If the request takes longer than `timeout`, the request will be aborted.
  timeout: 1000, // default is `0` (no timeout)

  // `withCredentials` indicates whether or not cross-site Access-Control requests
  // should be made using credentials
  withCredentials: false, // default

  // `adapter` allows custom handling of requests which makes testing easier.
  // Return a promise and supply a valid response (see lib/adapters/README.md).
  adapter: function (config) {
    /* ... */
  },

  // `auth` indicates that HTTP Basic auth should be used, and supplies credentials.
  // This will set an `Authorization` header, overwriting any existing
  // `Authorization` custom headers you have set using `headers`.
  // Please note that only HTTP Basic auth is configurable through this parameter.
  // For Bearer tokens and such, use `Authorization` custom headers instead.
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },

  // `responseType` indicates the type of data that the server will respond with
  // options are: 'arraybuffer', 'document', 'json', 'text', 'stream'
  //   browser only: 'blob'
  responseType: 'json', // default

  // `responseEncoding` indicates encoding to use for decoding responses (Node.js only)
  // Note: Ignored for `responseType` of 'stream' or client-side requests
  responseEncoding: 'utf8', // default

  // `xsrfCookieName` is the name of the cookie to use as a value for xsrf token
  xsrfCookieName: 'XSRF-TOKEN', // default

  // `xsrfHeaderName` is the name of the http header that carries the xsrf token value
  xsrfHeaderName: 'X-XSRF-TOKEN', // default

  // `onUploadProgress` allows handling of progress events for uploads
  // browser only
  onUploadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },

  // `onDownloadProgress` allows handling of progress events for downloads
  // browser only
  onDownloadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },

  // `maxContentLength` defines the max size of the http response content in bytes allowed in node.js
  maxContentLength: 2000,

  // `maxBodyLength` (Node only option) defines the max size of the http request content in bytes allowed
  maxBodyLength: 2000,

  // `validateStatus` defines whether to resolve or reject the promise for a given
  // HTTP response status code. If `validateStatus` returns `true` (or is set to `null`
  // or `undefined`), the promise will be resolved; otherwise, the promise will be
  // rejected.
  validateStatus: function (status) {
    return status >= 200 && status < 300; // default
  },

  // `maxRedirects` defines the maximum number of redirects to follow in node.js.
  // If set to 0, no redirects will be followed.
  maxRedirects: 5, // default

  // `socketPath` defines a UNIX Socket to be used in node.js.
  // e.g. '/var/run/docker.sock' to send requests to the docker daemon.
  // Only either `socketPath` or `proxy` can be specified.
  // If both are specified, `socketPath` is used.
  socketPath: null, // default

  // `httpAgent` and `httpsAgent` define a custom agent to be used when performing http
  // and https requests, respectively, in node.js. This allows options to be added like
  // `keepAlive` that are not enabled by default.
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // `proxy` defines the hostname, port, and protocol of the proxy server.
  // You can also define your proxy using the conventional `http_proxy` and
  // `https_proxy` environment variables. If you are using environment variables
  // for your proxy configuration, you can also define a `no_proxy` environment
  // variable as a comma-separated list of domains that should not be proxied.
  // Use `false` to disable proxies, ignoring environment variables.
  // `auth` indicates that HTTP Basic auth should be used to connect to the proxy, and
  // supplies credentials.
  // This will set an `Proxy-Authorization` header, overwriting any existing
  // `Proxy-Authorization` custom headers you have set using `headers`.
  // If the proxy server uses HTTPS, then you must set the protocol to `https`. 
  proxy: {
    protocol: 'https',
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },

  // `cancelToken` specifies a cancel token that can be used to cancel the request
  // (see Cancellation section below for details)
  cancelToken: new CancelToken(function (cancel) {
  }),

  // `decompress` indicates whether or not the response body should be decompressed 
  // automatically. If set to `true` will also remove the 'content-encoding' header 
  // from the responses objects of all decompressed responses
  // - Node only (XHR cannot turn off decompression)
  decompress: true // default

}
```

#### response

```js
{
  // `data` is the response that was provided by the server
  data: {},

  // `status` is the HTTP status code from the server response
  status: 200,

  // `statusText` is the HTTP status message from the server response
  // As of HTTP/2 status text is blank or unsupported.
  // (HTTP/2 RFC: https://www.rfc-editor.org/rfc/rfc7540#section-8.1.2.4)
  statusText: 'OK',

  // `headers` the HTTP headers that the server responded with
  // All header names are lower cased and can be accessed using the bracket notation.
  // Example: `response.headers['content-type']`
  headers: {},

  // `config` is the config that was provided to `axios` for the request
  config: {},

  // `request` is the request that generated this response
  // It is the last ClientRequest instance in node.js (in redirects)
  // and an XMLHttpRequest instance in the browser
  request: {}
}
```

#### Config Defaults

You can specify config defaults that will be applied to every request.

```js
// ### Global axios defaults

axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```

## nodemon

* By default, nodemon will only restart when a `.js` JavaScript file changes.

```sh
npm install -g nodemon
```

```json
// package.json
 "scripts": {
      "start": "node ./bin/www",
      "devstart": "nodemon ./bin/www",
      "serverstart": "DEBUG=express-locallibrary-tutorial:* npm run devstart"
    },
// We can now start the server using the devstart command
```

SYSTEM SETUP
===============

### integration with journald

#### create unit file

```ini
[Unit]
Description=payloadcmd

[Service]
WorkingDirectory=/home/frida/code/web/payloadcms/unonweb-5
ExecStart=npm run dev
Type=simple
Restart=always
RestartSec=10

StandardOutput=journal+console
StandardError=journal+console
SyslogLevelPrefix=true
SyslogLevel=info
SyslogFacility=daemon
```



```js
"<0>"  /* system is unusable */
"<1>"  /* action must be taken immediately */
"<2>"  /* critical conditions */
"<3>"  /* error conditions */
"<4>"  /* warning conditions */
"<5>"  /* normal but significant condition */
"<6>"  /* informational */
"<7>"  /* debug-level messages */
```

# VARIABLES

## environment variables

### NODE_ENV

#### set on commandline

```sh
export NODE_ENV=production # Linux and OSX
$env:NODE_ENV = 'production' # Windows

# set at application starting time
NODE_ENV=production node app.js # All platforms
```

#### set in .env file

requires the *dotenv* package

```ini
NODE_ENV=development
```

#### retrieve

```js
// retrieve
if (process.env.NODE_ENV === 'development') {
  // ...
}

if (process.env.NODE_ENV === 'production') {
  // ...
}
```

## commonjs variables

These variables are not available in ECMAScript module scope.

#### __dirname

```js
__dirname // The directory name of the current module
__dirname // "/Users/Sam/dirname-example/src/api"
path.dirname(__filename) // the same
process.cwd() // "/Users/Sam/dirname-example"

// usage example
const dirPath = path.join(__dirname, '/pictures')
fs.mkdirSync(dirPath)
```

#### __filename

The file name of the current module

```js
__filename // "/Users/mjr/example.js"
```

### esmodules

```js
/* How to replace __dirname and __filename in es6 modules */
import { dirname } from 'path'
import { fileURLToPath } from 'url'

const __filename = fileURLToPath(import.meta.url)
const __dirname = dirname(__filename)
```
