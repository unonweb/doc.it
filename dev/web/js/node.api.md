- https://nodejs.org/docs/latest-v22.x/api/index.html
# ASSERT

```js
const assert = require('assert');

// Use assert.deepStrictEqual to check if the objects are deeply equal
try {
  assert.deepStrictEqual(obj3, obj4);
  console.log('The objects are deeply equal');
} catch (error) {
  console.error(error);
}
```

```js
// assert.notDeepStrictEqual()
const assert = require('node:assert/strict');
assert.notDeepStrictEqual({ a: 1 }, { a: '1' });
// OK
```

# BUFFER

Buffer objects are used to represent a fixed-length sequence of bytes. Many Node.js APIs support Buffers.

The Buffer class is a subclass of JavaScript's Uint8Array class and extends it with methods that cover additional use cases. Node.js APIs accept plain `Uint8Arrays` wherever Buffers are supported as well.

While the Buffer class is available within the global scope, it is still recommended to explicitly reference it via an import or require statement.

```js
import { Buffer } from 'node:buffer'
const { Buffer } = require('buffer')

// Create a new buffer 
let newBuff = Buffer.from('New String')

// Convert a Buffer to JSON
let json = JSON.stringify(newBuff)

// Convert JSON to Buffer
Buffer.from(JSON.parse(json).data)

// Convert Buffer to Utf-8 String
newBuff.toString('utf8')
Buffer.from('Johan Ã–bert').toString();
```

# CHILD_PROCESS

- https://nodejs.org/docs/latest-v22.x/api/child_process.html
## callbacks vs listeners

Child processes can be consumed either using callbacks or listeners:

```js
// Consume using callbacks
const child = execFile('node', ['--version'], (error, stdout, stderr) => {
  if (error) throw error
  console.log(stdout)
})
// Consuming using listeners
const child = execFile('node', ['--version'])
child.stdout.on('data', (data) => { console.log(data) })
child.stderr.on('data', (data) => { console.log(data) })
```

## exec()

* useful when **only the final result** is the concern and where the child’s stdio streams are not required. 
* By default, the maximum buffer size is set to 200k but can be extended, else it will give error called `maxBuffer exceeded`.

`spawn()` returns a data stream whereas `exec()` would return buffer data and may encode it to read. Spawns a shell then executes the `command` within that shell, buffering any generated output.

```js
import { exec } from 'child_process'
const cmd = 'echo "The \\$HOME variable is $HOME"'
exec('cmd', { 
  cwd: process.cwd(),
  env: process.env,
  encoding: 'utf8',
  shell: '/bin/sh',
  timeout: 0,
  maxBuffer: 1024 * 1024,
}, (error, stdout, stderr) => {
  // this callback is called when process terminates
  if (error) {
    console.error(`exec error: ${error}`)
    console.log(error.code) // will be the exit code of the process
    return
  }
  if (stderr) {
	console.log(`stderr: ${stderr}`)
	}
  console.log(`stdout: ${stdout}`)
})
```

### promisified exec()

```js
// custom promisification
try {
  const cmd = `/home/payload/cms/_scripts/un-bundle-files ${ext}`
  const result = await execPromise(cmd)
  return result

} catch (error) {
  log(error.stack, user, __filename, 4)
}

function execPromise(cmd) {
  return new Promise((resolve, reject) => {
    exec(cmd, {
      cwd: destDir,
      encoding: 'utf8',
      shell: '/bin/sh',
      timeout: 0,
      maxBuffer: 1024 * 1024,
    }, (error, stdout, stderr) => {
				if (error) {
					return reject(error)
				}
				if (stderr) {
					return reject(stderr)
				}
				resolve(stdout)
})
```

```js
import { exec } from 'child_process'
import { promisify } from 'util'

const execPromise = promisify(exec)
execPromise('some-command') // do not await in scripts if not necessary
```

### shell expansion

The `command` string passed to the exec function is processed directly by the shell and special characters need to be dealt with accordingly:

```js
import { exec } from 'child_process'
const { exec } = require('child_process')

const cmd = 'echo "The \\$HOME variable is $HOME"'
exec(cmd, (error, stdout, stderr) => {
  // this callback is called when process terminates
  if (error) {
    console.error(`exec error: ${error}`)
    console.log(error.code) // will be the exit code of the process
    return
  }
  if (stderr) {
		console.log(`stderr: ${stderr}`)
	}
  console.log(`stdout: ${stdout}`)
})
```

## execSync()

```js
const result = execSync(cmd, {
    cwd: process.cwd() // Current working directory of the child process.
    encoding: 'buffer',
    timeout: undefined, 
    shell: '/bin/sh',
  })
```

```js
try {
	const result = execSync(cmd, {
    cwd: process.cwd() // Current working directory of the child process.
    encoding: 'buffer',
    timeout: undefined, 
    shell: '/bin/sh',
  })
  matches = result.split('\n') // returns an array even if there's no /n
	console.log(result)
} catch (error) {
    switch (error.code) {
				case 1:
					console.log('no processes matched or none of them could be signalled')
					break
				case 2:
					console.error('syntax error in the command line')
					break
				default:
					console.error('unknown error code: ', error.code)
					break;
			}
		}
```

```js
export default function pkillAllInstancesOf(fullCmd = '', user = '') {
	const cmd = `pkill -e -f "${fullCmd}"`
	log(user, __filename, `exec() ${cmd} ...`)

	if (sync === false) {
		const pkill = exec(cmd, (error, stdout, stderr) => {
			if (error) {
				console.log('no matching process running')
			}
			if (stderr) {
				console.error(`stderr: ${stderr}`)
			}
			if (stdout) {
				console.log(stdout)
			}
		})
	}
	else if (sync === true) {
		try {
			const pkill = execSync(cmd)
		} catch (error) {
			console.log('no matching process running')
		}

	}
}
```

### how to build commands

```js
let cmd 
cmd = 'echo "hello"' 
// executes '/bin/sh', '-c', 'echo "hello"'
cmd = 'echo "The \\$HOME variable is $HOME"' 
// The $HOME variable is escaped in the first instance, but not in the second.
cmd = 'ls *.json' 
// expands glob pattern
cmd = 'rsync --archive --human-readable --delete --progress ./_components/*.css ./_build/assets/styles/comp/'
// expands glob pattern (DO NOT USE QUOTATION MARKS around path)

exec(cmd, (error, stdout, stderr) => {
	console.log(`stdout:\n${stdout}`)
})
```

## execFile()

* Spawns the command directly without first spawning a shell by default
  Since the shell is not created,  some system commands may not work on all operating systems, `execFile('ls', ...)` works on Linux but the similar command `execFile('dir', ...)` will not work on Windows.

```js
const child = execFile('node', ['--version'], (error, stdout, stderr) => {
  if (error) {
    throw error
  }
  console.log(stdout)
})
```

```js
const util = require('node:util');
const execFile = util.promisify(require('node:child_process').execFile);
async function getVersion() {
  const { stdout } = await execFile('node', ['--version']);
  console.log(stdout);
}
getVersion();
```

```js
execFile('/usr/bin/uglifyjs', [...paths, '-c', '-m', '-o', destPath], (error, stdout, stderr) => {
  if (error) {
    throw error;
  }
  console.log(stdout);
});
```

## spawn()

- https://nodejs.org/docs/latest-v22.x/api/child_process.html#child_processspawncommand-args-options
-
Designed to run **system** **commands**, where this command is sent to run on new process but nothing is done in the main node process.

```js
const { spawn } = require("child_process")
const ls = spawn("ls", ["-la"])

const ping = spawn('ping', ["google.com"])

ping.stdout.on('data', data => {
	// 'data' event is fired every time data is output from the cmd
  console.log(`stdout: ${data.toString()}`)
})
ping.stderr.on('data', data => {
  // 'data' event is fired every time data is output from the cmd
  console.error(`stderr: ${data}`)
})
```

```js
// Example of checking for failed spawn:

const { spawn } = require('node:child_process');
const subprocess = spawn('bad_command');

subprocess.on('error', (err) => {
  console.error('Failed to start subprocess.');
}); 
```

### background process

* `unref()`
* `detached: true`

When a child process is invoked the parent keeps waiting for it to finish before finishing itself.
By default, the parent will wait for the detached child to exit. 
To prevent the parent from waiting for a given `subprocess` to exit, use `subprocess.unref()`

When using the `detached` option to start a long-running process, the process will not stay running in the background after the parent exits unless it is provided with a `stdio` configuration that is not connected to the parent.

```js
var path = require('path');
var spawn = require('child_process').spawn

// a background process is running!
// it is not stopped even if parent node process is killed.
spawn(cmd, args, {
  detached: true,
  stdio: 'ignore', // a stdio configuration that is independent from the NodeJS process; piping all stdio to /dev/null
  env: process.env,
}).unref()

var relativFilename = '../../node_modules/bla/bla/bla.js';

spawn('node', [Path.resolve(__dirname, relativeFilename)], {detached: true, stdio: 'ignore'}).unref();
process.exit(0);
```

### on spawn

* is emitted once the child process has spawned successfully
* if the child process does not spawn successfully, `'spawn'` is not emitted and `'error'` is emitted instead
* will fire regardless of whether an error occurs **within** the spawned process

```js
ping.on('spawn', () => {
  console.log(`child process spawned successfully.`)
})
```

### on close

* is emitted after a process has ended *and* the stdio streams of a child process have been closed

```js
ping.on('close', code => {
  console.log(`child process exited with code ${code}`)
})
```

### on exit

* is emitted after the child process ends
* when 'exit' is triggered, child process stdio streams might still be open

The `'exit'` event may or may not fire after an error has occurred. When listening to both the `'exit'` and `'error'` events, guard against accidentally invoking handler functions multiple times.

```js
ping.on('exit', code => {
  console.log(`Child exited with code ${code}`)
})
```

### on error

is emitted whenever:

* The process could not be spawned.
* The process could not be killed.
* Sending a message to the child process failed.
* The child process was aborted via the signal option.

```js
ping.on('error', err => {
  console.error('Failed to start subprocess. Error: ', err)
})
```





```js
const { spawn } = require('node:child_process')
const ls = spawn('ls', ['-lh', '/usr'])

ls.stdout.on('data', (data) => {
  console.log(`stdout: ${data}`)
})

ls.stderr.on('data', (data) => {
  console.error(`stderr: ${data}`)
})

ls.on('close', (code) => {
  console.log(`child process exited with code ${code}`)
})
```

```js
const eleventy = spawn('npx', 
 	['eleventy', `--config="${eleventyConfig}"`, '--incremental', '--ignore-initial'], 
	{ shell: true, cwd: eleventyDir }
)

eleventy.stdout.on('data', output => console.log(output.toString()))
eleventy.stderr.on('data', output => console.error(output.toString()))
```

#### passing input to commands with *stdin*

In the previous examples, we executed commands without giving any input (or providing limited inputs as arguments). In most cases, input is given through the STDIN stream.

```sh
echo "1. pear\n2. grapes\n3. apple\n4. banana\n" | grep apple # 3. apple
```

Here, the input is passed to the `grep` command through STDIN. In this case the input is a list of fruit, and `grep` filters the line that contains `"apple"`

The child process object returned by the `spawn` function provides us with an input stream which we can write into. Let’s use it to pass input to a `grep` child process:

```js
const { spawn } = require('node:child_process')
// run the grep command
const command = spawn('grep', ["apple"])

// use the stdin stream from the command to
// send data to the spawned command
command.stdin.write("1. pear\n")
command.stdin.write("2. grapes\n")
command.stdin.write("3. apple\n")
command.stdin.write("4. banana\n")
// once we're done sending input, call the `end` method
command.stdin.end()

// similar to the previous example, print the output whenever it's 
// received
command.stdout.on('data', output => {
    console.log("Output: ", output.toString())
})
```



## exec() vs spawn()

* `exec()` creates a shell first and then executes the command. 

The key difference between `exec()` and `spawn()` is how they return the data. 
As `exec()` stores all the output in a buffer, it is more memory intensive than `spawn()`, which streams the output as it comes.

use `exec()`

* if you want to execute simple commands that don’t usually give too much output
* if you are not expecting large amounts of data to be returned (creating a folder, getting the status of a file)
* if you need shell processing (like wildcards or command chaining)

use `spawn()`

* if you are expecting a large  amount of output from your command (using command to manipulate binary data and then loading it in to your Node.js program)
* for functions with continuous or long-running output

```js
// options 
// {cwd: <string>} working directory of the child process. Default: process.cwd()
child_process.exec('shell command', { cwd: '/path/to/some/dir' }, (err, stdout, stderr) => {
  
})
```

## fork()

* used specifically to spawn new Node.js processes

Built on top of `spawn()` will **spin up a fresh V8 instance** to be used to run any Node-based code, either of the same program or  another. This is useful to execute CPU intensive tasks in parallel or to scale an application. Another difference between `spawn` is that this will have an IPC channel built-in.

```js
// main.js
const { fork } = require("child_process");

console.log("Running main.js");
console.log("Forking a new subprocess....");

const child = fork("child.js");
child.send(29); // send msg to child

child.on("message", function (message) { // receive msg from child
  console.log(`Message from child.js: ${message}`);
});
```

```js
// child.js
process.on("message", function (message) { // receive msg from parent
  console.log(`Message from main.js: ${message}`);
});

process.send("Nathan"); // send msg to parent
```

# CONSOLE

## console.time()

```js
console.time('label') // Starts a timer that can be used to compute the duration of an operation. Timers are identified by a unique label. Use the same label when calling console.timeEnd() to stop the timer and output the elapsed time
console.timeEnd('label')
```

# CRYPTO

## createHash()

```sh
openssl list -digest-algorithms # display the available digest algorithms.
```



```js
crypto.createHash( algorithm, options ) // Creates and returns a Hash object that can be used to generate hash digests

const hash = crypto.createHash('md5').update('somestring').digest('hex'); // 9b74c9897bac770ffc029102a200c5de

function(somestring = '') {
    return crypto.createHash('md5').update(somestring).digest('hex').toString();
}
```

```js
const crypto = require('crypto')
const data = 'some data to be hashed'
const hash = crypto.createHash('sha256')
hash.update(data) // used to feed data to the hash object; can be called multiple times to update the hash with more data
const digest = hash.digest('hex') // used to generate the hash of the data that has been fed to the hash object; takes an optional encoding argument that specifies the encoding of the output. The default encoding is binary, but you can also use hex, base64, etc.

console.log(digest); // prints the hash of the data in hexadecimal format
```

# ERRORS

[Node Docs](https://nodejs.org/dist/latest-v18.x/docs/api/errors.html)

### class Error

#### error.code

[List of Error Codes](https://nodejs.org/dist/latest-v18.x/docs/api/errors.html#nodejs-error-codes)

The `error.code` property is a string label that identifies the kind of error. `error.code` is the most stable way to identify an error. It will only change between major versions of Node.js. In contrast, `error.message` strings may change between any versions of Node.js.

### errors in callbacks

This will not work because the callback function passed to `fs.readFile()` is called asynchronously. By the time the callback has been called, the surrounding code, including the `try…catch` block, will have already exited. Throwing an error inside the callback **can crash the Node.js process** in most cases. If *domains*  are enabled, or a handler has been registered with `process.on('uncaughtException')`, such errors can be intercepted.

```js
// THIS WILL NOT WORK:
const fs = require('node:fs');

try {
  fs.readFile('/some/file/that/does-not-exist', (err, data) => {
    // Mistaken assumption: throwing here...
    if (err) {
      throw err;
    }
  });
} catch (err) {
  // This will not catch the throw!
  console.error(err);
}
```

# FETCH

```js
const fs = require('')
let res = await fetch(imageUrl)
res.body.pipe(fs.createWriteStream('./path/to/image.png'))
```

FS
=========

* provides functions to interact with the file system
* all the methods are asynchronous by default
* `const fs = require("fs");`

## solutions

### saveAsJson

```js
import { promises as fsPromises } from 'fs'
import { existsSync } from 'fs'
import path from 'path'

async function saveAsJson(jsonStr, destPath) {
	try {
		if (typeof jsonStr === 'object') {
			jsonStr = JSON.stringify(jsonStr) // convert json if obj
		}
		destPath += '.json' // add file extension
		if (!existsSync(destPath)) {
			let destDir = path.dirname(destPath)
			await fsPromises.mkdir(destDir, { recursive: true })
			console.log('path created: ', destPath)
		}
		await fsPromises.writeFile(destPath, jsonStr)
		console.log('successfully written json to: ', destPath)
	} catch (err) {
		console.error(err)
	}
}
```

### saveAsModule

```js
import { promises as fsPromises } from 'fs'
import { existsSync } from 'fs'
import path from 'path'

async function saveAsModule(destPath, data) {
	try {
		if (typeof data === 'object') {
			data = JSON.stringify(data) // convert obj into string
		}
		
		let moduleStr = `export default ${data}`

		if (path.extname(destPath) === '') {
			destPath += '.js' // add file extension	if not given
		}

		// create path if not existent:
		if (!existsSync(destPath)) {
			let destDir = path.dirname(destPath)
			await fsPromises.mkdir(destDir, { recursive: true })
			console.log('path created: ', destPath)
		}
		
		// write locally
		//await fsPromises.writeFile(destPath, moduleStr)
		await fsPromises.appendFile(destPath, moduleStr)
		console.log('successfully written module to: ', destPath)
	} catch (err) {
		console.error(err)
	}
}
```

### saveArray

```js
import { promises as fsPromises } from 'fs'
import { existsSync } from 'fs'
import path from 'path'

async function saveArray(destPath, data = []) {
	try {
		
		// empty file if already existent
		if (existsSync(destPath)) {
			await fsPromises.writeFile(destPath, '')
			console.log('file already exists - overwrite it with empty string')
		}

		// add file extension if not present
		if (path.extname(destPath) === '') {
			destPath += '.js'
		}

		// create path if not existent:
		if (!existsSync(destPath)) {
			let destDir = path.dirname(destPath)
			await fsPromises.mkdir(destDir, { recursive: true })
			console.log('path created: ', destPath)
		}
		
		// write locally
		for (let line of data) {
			await fsPromises.appendFile(destPath, `${line}\n`)
		}
		console.log('successfully written array to: ', destPath)
	} catch (err) {
		console.error(err)
	}
}
```

### create empty file

```js
const fs = require('fs')

// create an empty file
fs.open('file.txt', 'w', (err, file) => {
  if (err) {
    throw err
  }
  console.log('File is created.')
})

// If you do not need the file descriptor for further processing, just wrap the call in fs.closeSync() to close the file automatically:
fs.closeSync(fs.openSync('file.txt', 'w'))
```

### callbacks vs promises

```js
/* PROMISES */
const fs = require('fs').promises // contains a subset of the interface that's on fs, but with promise-based interfaces

import { unlink } from 'node:fs/promises' // imports 'unlink' from the promises API

try {
  await unlink('/tmp/hello')
  console.log('successfully deleted /tmp/hello')
} catch (error) {
  console.error('there was an error:', error.message)
}

/* CALLBACK */
import { unlink } from 'node:fs' // imports 'unlink' from the callbck

unlink('/tmp/hello', (err) => {
  if (err) throw err
  console.log('successfully deleted /tmp/hello')
})
```

## promises API

### import

```js
// import everything
import { promises as fsPromises } from 'fs'
const fsPromises = require('fs').promises

// import parts
import { rm } from 'fs/promises'
```

### overview

```js
const { readFile } = require("fs").promises

/* THEN */
fs.readFile("file.txt", "utf8")
    .then(text => console.log("The file contains:", text))

/* ASYNC / AWAIT */

// Example: Read a file and change its content and read
// it again using promise-based API.
const fs = require('fs/promises')

async function example() {
  const fileName = '/Users/joe/test.txt'
  try {
    const data = await fs.readFile(fileName, 'utf8')
    console.log(data)
    const content = 'Some content!'
    await fs.writeFile(fileName, content)
    console.log('Wrote some content!')
    const newData = await fs.readFile(fileName, 'utf8')
    console.log(newData)
  } catch (err) {
    console.log(err)
  }
}
example()
```

## callback API

```js
fs.access() // check if the file exists and Node can access it with its permissions
fs.copyFile() // copies a file

fs.stat(path, options, callback)
// callback: gets two arguments `(err, stats)` where stats is an fs.Stats object (such object provides information about a file.)

fs.statSync(path, options)
fs.renameSync(oldPath, newPath)
fs.unlink(path, callback)
// will not work on a directory
```

## fs.access()

```js
import { promises as fsPromises } from 'fs'

const fsPromises.access('path')
// Fulfills with 'undefined' upon success
await fsPromises.access('path', mode)
```

```js
import { access, constants } from 'node:fs/promises';
// checks if the file `/etc/passwd` can be read and written by the current process.
try {
  await access('/etc/passwd', constants.R_OK | constants.W_OK);
  console.log('can access');
} catch {
  console.error('cannot access');
}
```

```js
import { promises as fsPromises } from 'fs'

async function exists (path) {  
  try {
    await fsPromises.access(path)
    return true
  } catch {
    return false
  }
}

// Example:
const Path = require('path')  
const path = Path.join(__dirname, "existing-file.txt")

await exists(path)  // true
```

## fs.accessSync()

```js
import { accessSync, constants } from 'node:fs';

try {
  accessSync('etc/passwd', constants.R_OK | constants.W_OK);
  console.log('can read/write');
} catch (err) {
  console.error('no access!');
}
```

## fs.appendFile()

```js
fs.appendFile() // append data to a file. If the file does not exist, it’s created
```

#### appendFile()

Asynchronously append data to a file, creating the file if it does not yet exist. data can be a string or a Buffer.

* good for occasional appends
* creates a new file handle each time it's called
* opens a file handle for each piece of data you add to your file

```js
fsPromises.appendFile('path', data[, options])
```

## fs.cp()

* if the dest directory does not exist, it will be created automatically (no matter how many levels of directories).
* you can copy the entire folder, including the subdirectories, without having to do it recursively and separately

```js
fs.cp('./a.txt', './aa/b.txt', (err) => {
  if (err) {
    console.error(err);
  }
})

// Copy the entire directory, including subdirectories:
fs.cp('./aa', './bb', { recursive: true }, (err) => {
  if (err) {
    console.error(err)
  }
})
```

## fs.copyFileSync()

```js
import { copyFileSync, constants } from 'node:fs';

// destination.txt will be created or overwritten by default.
copyFileSync('source.txt', 'destination.txt');

// By using COPYFILE_EXCL, the operation will fail if destination.txt exists.
copyFileSync('source.txt', 'destination.txt', constants.COPYFILE_EXCL); 
```

## fs.copyFile

* when you use copyFile(), you must make sure that the directory exists, if not, you need to use `fs.mkdir()` or `fs.mkdirSync()` to create the directory.

```js
import { copyFile, constants } from 'fs/promises';
// destination.txt will be created or overwritten by default.
copyFile('source.txt', 'destination.txt', mode = 0)

try {
  await copyFile('source.txt', 'destination.txt');
  console.log('source.txt was copied to destination.txt');
} catch {
  console.error('The file could not be copied');
}

// By using COPYFILE_EXCL, the operation will fail if destination.txt exists.
try {
  await copyFile('source.txt', 'destination.txt', constants.COPYFILE_EXCL);
  console.log('source.txt was copied to destination.txt');
} catch {
  console.error('The file could not be copied');
} 
```

## fs.cpSync()

Synchronously copies the entire directory structure from `src` to `dest`, including subdirectories and files.

```js
import { cpSync } from 'fs'
fs.cpSync(sourceDir, destDir, { recursive: true })
```

## fs.existsSync()

Returns `true` if the path exists, `false` otherwise.

```js
import { existsSync } from 'node:fs';
import { existsSync } from 'fs'
const { existsSync } = require('fs')

if (existsSync('/etc/passwd')) {
  console.log('The path exists.')
}
```

## fs.mkdir()

* if `recursive: false` calling `mkdir` results in a rejection when `path` is a directory that exists

```js
import { mkdir } from 'fs/promises';

fsPromises.mkdir('path', { 
  recursive: false 
})

try {
  const createDir = await mkdir('./test/project/assets', { recursive: true }) 
	// recursive: true 
	// parent directories should be created
  console.log(`created ${createDir}`)
} catch (err) {
  console.error(err.message)
}
```

## fs.mkdirSync()

```js
mkdirSync('path', { recursive: false } )
```

## fs.open()

```js
fs.open(path, flags, mode, callback) // used to create, read, or write a file
// flags indicate the behavior of the file to be opened.  
	// r, r+, rs, rs+, w, wx, w+, wx+, a, ax, a+, ax+
// mode: sets the mode of file; default: readwrite
	// r-read
	// w-write
	// r+
	// -readwrite
// callback: receives two arguments (err, data), where data is the contents of the file.

const fs = require('fs')
```

```js
// create an empty file
fs.open('file.txt', 'w', (err, file) => {
  if (err) {
    throw err
  }
  console.log('File is created.')
})
```

## fs.openSync()

```javascript
// create an empty file
try {
  const file = fs.openSync('file.txt', 'w')

  console.log('File is created.')
} catch (error) {
  console.log(error)
}
```

## fs.readFile()

### promises

```js
const fsPromises = require('fs/promises')
import { promises as fsPromises } from 'fs'

fsPromises.readFile(path, { 
  encoding: null 
})
// If no encoding is specified, the data is returned as a <Buffer> object. 
// With encoding the data will be a string.
```

```js
const lines = (await readFile(path, 'utf8')).split('\n') // split file content into array of lines
```

```js
fsPromises.readFile(path, 'utf-8')
// If options is a string, then it specifies the encoding.

try {
	let result = await fsPromises.readFile('/home/frida/code/web/projects/sites/haerer/node/_src/ssr-result.js', 'utf-8')
	console.log(result)
} catch (error) {
	console.error(error)
}
```

### callback

```js
fs.readFile(path, options, callback) // read the content of a file. Related: `fs.read()`
// options.encoding: 'utf8'
// callback: receives two arguments (err, data), where data is the contents of the file.

// example:
fs.readFile('/etc/passwd', (err, data) => {
	if (err) throw err
	console.log(data)
})

// read json :
fs.readFile('./myJson.json', 'utf8', (err, data) => {
  if (err) throw new Error(err)
  // parse JSON string to JSON object
  const myJson = JSON.parse(data)
  return myJson
})
```

## fs.readFileSync()

* if no encoding is specified, then the raw buffer is returned
* if `options` is a string, then it specifies the encoding

```js
fs.readFileSync(path, { options })
let data = fs.readFileSync('input.txt')
console.log("Synchronous read: " + data.toString())
```

```js
fs.readFileSync(filename).toString('UTF8').split('\n');
fs.readFileSync(filename, 'utf8').split('\n');
```

## fs.readdir()

```js
fs.readdir(path, options, callback) // Reads the contents of a directory
// callback: gets two arguments `(err, files)` where files is an array of the names of the files in the directory
```

```js
// defaults
fsPromises.readdir(path, { 
  encoding: 'utf8',
  withFileTypes: false,
  recursive: false,
})
```

```js
// with defaults
try {
  const files = await readdir(path);
  for (const file of files) {
   console.log(file); 
  }
} catch (err) {
  console.error(err);
}
```

```js
// return an array of <fs.Dirent> objects from the directory
try {
  const files = await fsPromises.readdir(path, { encoding: 'utf8', withFileTypes: true,	recursive: false })
  for (const file of files) {
  	// file.path --> '/srv/web/resources/img/background/pexels'
		// file.name --> 'adrien-olichon-2387532.jpg'
		// file.isSymbolicLink()
		// file.isDirectory()
		// file.isFile() --> Returns true if the <fs.Dirent> object describes a regular file.
  }
} catch (err) {
  console.error(err);
}
```

```js
fs.readdir(__dirname,  { 
  withFileTypes: true
}, (err, files) => { 
  console.log("\nCurrent directory files:"); 
  if (err) 
    console.log(err)
  else { 
    files.forEach(file => console.log(file)) 
  } 
}) 
```

## fs.readdirSync()

```js
readdirSync('path', {
  encoding: 'utf8',
  withFileTypes: false,
  recursive: false
}
```

```js
const files = fs.readdirSync('C:/tmp').filter(fn => fn.endsWith('.csv'));
```

## fs.rm()

```js
fsPromises.rm(path, { force: false, recursive: false })
// path <string> | <Buffer> | <URL>
// force: true # exceptions will be ignored if path does not exist
// recursive: true # perform a recursive directory removal. In recursive mode operations are retried on failure.

fsPromises.rm(path, { recursive: true, force: true }) // corresponds to "rm -rf"
```

```js
const { readdirSync, rmSync } = require('fs');
const dir = './dir/path';
readdirSync(dir).forEach(file => rmSync(`${dir}/${file}`));
```

```js
// remove all files in directory (async)
import fs from "node:fs/promises";
import path from "node:path";

const directory = "test";

for (const file of await fs.readdir(directory)) {
  await fs.unlink(path.join(directory, file));
}
```

## fs.rmdir()

```js
fsPromises.rmdir(path, { 
  recursive: false, 
  maxRetries: 0, 
  retryDelay: 100 
})

// To get a behavior similar to rm -rf
// use 

fsPromises.rm(path, { 
  recursive: true, 
  force: true 
})
```

## fs.rename()

* use to rename or move files

```js
const fsPromises = require('fs/promises')
fsPromises.rename(oldPath, newPath) //
```

## fs.stat()

* https://nodejs.org/docs/latest-v20.x/api/fs.html#fspromisesstatpath-options
* follows symbolic links

```js
import { stat } from 'fs/promises'

stat('path') // 
const { mtime, ctime } = stat('file')

const stat = await fsPromises.stat('file')
stat.mtimeMs // returns a timestamp that indicates the last time the given file was modified in milliseconds since the POSIX epoch
```

```js
import { stat as fsStat } from 'fs/promises'
try {
 	const stat  = await fsStat('.')
	return stat.isDirectory() 
} catch (error) {
  
}
```

```js
 if (!await canAccess(pathVarCSS) || doc.css.var_timestamp > (await stat(pathVarCSS)).mtimeMs) {
   
 }
```

## fs.statSync()

```js
fs.statSync('./un-btn.js').mtimeMs // 1701847377624.959
```

## fs.unlink()

```js
fs.unlink('fileToBeRemoved', (err) => {
    if(err && err.code == 'ENOENT') {
        // file doens't exist
        console.info("File doesn't exist, won't remove it.")
    } else if (err) {
        // other errors, e.g. maybe we don't have enough permission
        console.error("Error occurred while trying to remove file")
    } else {
        console.info(`removed`)
    }
})
```

## fs.unlinkSync()

* returns `undefined`

```js
fs.unlinkSync(path) // removes a file or symbolic link
```

## fs.watchFile()

Watch Only Files For Detecting Changes

* `curr`: The curr parameter represents the current state of the file. 
  Contains an instance of the `fs.Stats` object that provides information about the file's current attributes, such as file size, modification timestamp, and permissions.
* `prev`: The prev parameter represents the previous state of the file. It also contains an instance of the `fs.Stats` object, representing the file's attributes before the latest change.

```js
const fs = require('fs');

// Specify the file to watch
const filename = 'path/to/file.txt';

// Watch the file for changes
fs.watchFile(filename, (curr, prev) => {
    // File has been modified
    console.log('File modified');
    // Perform actions or update information accordingly
});
```

## fs.watch()

provides a more general-purpose solution for monitoring both files and  directories. It takes a file or directory path as an argument and  registers a listener function to handle various types of events, such as change, rename, or error. 

This method is a little bit different because the callback function doesn't provide any information about the content or metadata of the  changed file. It can be used to detect cases such as renaming, content  appending, or removing, etc.

```js
const fs = require('fs');

// Specify the file or directory to watch
const path = 'path/to/file.txt';

// Watch the file or directory for changes
fs.watch(path, { recursive: true }, (eventType, filename) => {
    // Event occurred
    console.log(`Event type: ${eventType}`);
    if (filename) {
        console.log(`File affected: ${filename}`);
    }
    // Perform actions or update information accordingly
});
```

## fs.writeFile()

* https://nodejs.org/docs/latest-v20.x/api/fs.html#fspromiseswritefilefile-data-options

Returns a promis which fulfills with `undefined` upon success.

* a high-level method for writing a bunch of data you have in RAM to a file
* asynchronously writes data to a file, replacing the file if it already exists
* promise is resolved with no arguments upon success
* don't use it for large files or performance-critical stuff
* use it if you write out small JSON files or so in your code
* returns a promise that fulfills with `undefined` upon success.

```js
import { writeFile } from 'fs/promises'
    
writeFile('file', data, {
  encoding: 'utf8'
})

writeFile('file', data, 'utf8') //

const promise = ('log.txt', 'Hello Node', { flag: "a+" })
```

```js
/* flags:
		'a': Open file for appending. The file is created if it does not exist.
		'ax': Like 'a' but fails if the path exists.
		'a+': Open file for reading and appending. The file is created if it does not exist.
		'ax+': Like 'a+' but fails if the path exists.
		'w': Open file for writing. The file is created (if it does not exist) or truncated (if it exists).
		'wx': Like 'w' but fails if the path exists.
		'w+': Open file for reading and writing. The file is created (if it does not exist) or truncated (if it exists).
		'wx+': Like 'w+' but fails if the path exists. 
	*/
```

```js
import { existsSync } from 'fs'

if (!existsSync(path)) {
  let destDir = dirname(path)
  await mkdir(destDir, { recursive: true })
	log(`path created: ${path}`, user, __filename, 7)
}
```

## fs.writeFileSync()

```js
fs.writeFileSync(file, data, options)
```

### writeStream vs writeFile

Does the data fit easily in RAM? If it does, then `writeFile` is certainly simpler to use (and potentially a slightly bit faster); else, you better use streams and write data in chunks.

`fs.WriteStream` is a stream that opens the file in the  background and queues writes until the file is ready. Also, as it  implements the stream API, you can use it in a more generic way, just  like a network stream or so. You'll e.g. want this when a user uploads a file to your server - take the incoming HTTP POST stream, `pipe()` it to the `WriteStream`. Very easy.

The main difference between `fs.createReadStream()` and `fs.writeFile()` is that one accepts a stream and one accepts a in-memory resource. 
Therefore if you have a large file, it is best to use a stream so only small chunks are loading into memory at one time.

## fs.dirent

```js
// dirent: A representation of a directory entry, which can be a file or a subdirectory within the directory
dirent.path
dirent.name
dirent.isSymbolicLink()
dirent.isDirectory()
dirent.isFile()
```

## fs.Stats

```js
Stats {
  dev: 2114,
  ino: 48064969,
  mode: 33188,
  nlink: 1,
  uid: 85,
  gid: 100,
  rdev: 0,
  size: 527,
  blksize: 4096,
  blocks: 8,
  atimeMs: 1318289051000.1,
  mtimeMs: 1318289051000.1,
  ctimeMs: 1318289051000.1,
  birthtimeMs: 1318289051000.1,
  atime: Mon, 10 Oct 2011 23:24:11 GMT,
  mtime: Mon, 10 Oct 2011 23:24:11 GMT,
  ctime: Mon, 10 Oct 2011 23:24:11 GMT,
  birthtime: Mon, 10 Oct 2011 23:24:11 GMT 
}
```

# GLOBALS

## global

In browsers, the top-level scope is the global scope. This means that within the browser `var something` will define a new global variable. 
In Node.js this is different. The top-level scope is not the global scope; `var something` inside a Node.js module will be local to that module.

```js
global.globalString = "This can be accessed anywhere!";
console.log(globalString); // Output: "This can be accessed anywhere!"
```

HTTP
======

* `http.createServer(options, requestListener)` # Returns a new instance of http.Server
  * *requestListener*: is a function which is automatically added to the 'request' event; it is called every time a client connects to the server
    * `(request, response) => { function body }` # The request and response bindings are objects representing the incoming and outgoing data

```js
const {createServer} = require("http");
let server = createServer((request, response) => {
    response.writeHead(200, {"Content-Type": "text/html"});
    response.write(`
            <h1>Hello!</h1>
            <p>You asked for <code>${request.url}</code></p>`);
    response.end();
});
server.listen(8000);
console.log("Listening! (port 8000)");
```

## request()

**`http.request(url, options, callback)`** # returns an instance of the *http.ClientRequest* class

* *options*: configures the request
  * `headers: {}` # An object containing request headers.
  * `method: "GET"` # Default: 'GET'
  * `path: '/index.html?page=12'` # Default: '/'
  * `host: 'domain.com'`| `host: 172.217.22.67` # Default: 'localhost'
  * `protocol: 'http'` # Default: 'http:'

```js
  const {request} = require("http");
  let requestStream = request({
    hostname: "eloquentjavascript.net",
    path: "/20_node.html",
    method: "GET",
    headers: {Accept: "text/html"}
  }, response => {
    console.log("Server responded with status code", response.statusCode);
  }); 
  requestStream.end();
```


## Class: http.Server

**`server.listen(port, host, backlog, callback)`** # Starts the HTTP server listening for connections.

* *host*: if omitted, the server will accept connections on the unspecified IPv6 address (::) or IPv4 (0.0.0.0)


## Class: http.ServerResponse

**`response.write(chunk, encoding, callback)`** # sends a chunk of the response body

* may be called multiple times to provide successive parts of the body.
* *chunk*: can be a string or a buffer. If chunk is a string, the second parameter specifies how to encode it into a byte stream.


**`response.writeHead(statusCode, statusMessage, headers)`** # Sends a response header to the request

*  must only be called once on a message
*  must be called before `response.end()` is called.

```js
  response.writeHead(200, {"Content-Type": "text/html"}); // inform the client that we’ll be sending back an HTML document
```


**`response.end(data, encoding, callback)`** # signals to the server that all of the response headers and body have been sent

* must be called on each response


## Class: http.ClientRequest

* `request.path`  
* `request.method`  
* `request.host`  
* `request.protocol`

# HTTPS

```js
// curl -k https://localhost:8000/
const https = require('node:https');
const fs = require('node:fs');

const options = {
  key: fs.readFileSync('test/fixtures/keys/agent2-key.pem'),
  cert: fs.readFileSync('test/fixtures/keys/agent2-cert.pem'),
};

https.createServer(options, (req, res) => {
  res.writeHead(200);
  res.end('hello world\n');
}).listen(8000); 
```



```js
// GET request
const https = require('https')

const reqQpts = {
  hostname: 'example.com',
  port: 443,
  path: '/todos',
  method: 'GET',
};

const req = https.request(reqQpts, res => {
  console.log(`statusCode: ${res.statusCode}`)

  res.on('data', d => {
    process.stdout.write(d)
  })
})

req.on('error', error => {
  console.error(error)
})

req.end()

// POST request

const data = JSON.stringify({
  todo: 'Buy the milk',
});

const options = {
  hostname: 'whatever.com',
  port: 443,
  path: '/todos',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': data.length,
  },
};

const req = https.request(options, res => {
  console.log(`statusCode: ${res.statusCode}`);

  res.on('data', d => {
    process.stdout.write(d);
  });
});

req.on('error', error => {
  console.error(error);
});

req.write(data);
req.end();
```

OS
========

```js
os.homedir() // Returns the path to the home directory of the current user.
os.hostname() // Return the hostname.
os.networkInterfaces() // Returns the details of the network interfaces available on your system.
os.userInfo() // Returns information about the current user
os.type() // Identifies the operating system
os.totalmem() // Returns the number of bytes that represent the total memory available in the system.
os.tmpdir() // Returns the path to the assigned temp folder.
os.release() // Returns a string that identifies the operating system release number
```

PATH
============

* https://nodejs.org/docs/latest-v20.x/api/path.html

```js
const path = require('path')
import path from 'path'
```

## basename()

```js
import { basename } from 'path'

path.basename('path', 'suffix') // Returns the last portion of a path

// suffix: an optional suffix to remove
basename('/foo/bar/baz/asdf/quux.html') // 'quux.html'
basename('/foo/bar/baz/asdf/quux.html', '.html') // 'quux'

// get name of parent directory:
basename(path.dirname('/sites/rjuschka/11ty/src')) // 11ty

basename('/test/something') // something
basename('/test/something.txt') // something.txt
basename('/test/something.txt', '.txt') // something

basename('/test/something.txt', extname('/test/something.txt')) // something
```

## dirname()

```js
import { dirname } from 'path'

path.dirname()  // Return the directory part of a path
path.dirname('/foo/bar/baz/asdf/quux') // '/foo/bar/baz/asdf'

__dirname // The directory name of the current module
__dirname // "/Users/Sam/dirname-example/src/api"
path.dirname(__filename) // the same
```

## resolve()

```js
import path from 'path'
//const path = require("path")

path.resolve() // resolves a sequence of paths or path segments into an absolute path.
path.resolve('/foo/bar', './baz') // returns '/foo/bar/baz'
path.resolve(path.dirname('')) // gives the working dir
```

## parse()

* parses a path to an object with the segments that compose it

```js
import path from 'path' 
//const path = require('path')

path.parse()
// root: the root
// dir: the folder path starting from the root
// base: the file name + extension
// name: the file name
// ext: the file extension

const filename = 'hello.html'
path.parse(filename).name  // "hello"
path.parse(filename).ext   // ".html"
path.parse(filename).base	// "hello.html"

require('path').parse('/users/test.txt')
// results in
{
  root: '/',
  dir: '/users',
  base: 'test.txt',
  ext: '.txt',
  name: 'test'
}
```

## extname()

```js
import path from 'path'
const path = require("path")

path.extname()  // Return the extension part of a path
```

## join()

```js
import path from 'path'
//const path = require("path")

path.join()     // Joins two or more parts of a path
```

# PROCESS

## env

```js
import { env } from 'process'

env.foo = 'bar'
console.log(env.foo)

process.env.PWD // the working directory when the process was started. This stays the same for the entire process.
```

## exit()

```js
process.exit(1) // exit with a 'failure' code
```

`process.exit()` will force the process to exit as quickly as possible even if there are still asynchronous operations pending that have not yet completed fully, including I/O operations to `process.stdout` and `process.stderr`.

In most situations, it is not actually necessary to call `process.exit()` explicitly. The Node.js process will exit on its own if there is no additional work pending in the event loop. The `process.exitCode` property can be set to tell the process which exit code to use when the process exits gracefully.

```js
  process.exitCode = 1
```

## chdir()

```js
import { chdir, cwd } from 'node:process';

console.log(`Starting directory: ${cwd()}`);
try {
  chdir('/tmp');
  console.log(`New directory: ${cwd()}`);
} catch (err) {
  console.error(`chdir: ${err}`);
}
```

```js
chdir(dirname(process.argv[1])) // change to script location directory
```

## cwd()

```js
import { cwd } from 'process'
process.cwd() // the current working directory. It reflects changes made via process.chdir().
```

## kill()

* Sending signal `0` can be used as a platform independent way to test for the existence of a process.

```js
process.kill(1000)
// not specifying a signal automatically sends [SIGTERM] to process id 1000
```

```js
import process from 'process';

export default function isRunning(pid) {
	// returns true if process following pid is running
	try {
		return process.kill(pid, 0); // As a special case, a signal of 0 can be used to test for the existence of a process.
	} catch (error) {
		console.error(`pid ${pid} not found`);
		return error.code === 'EPERM';
	}
}
```

```js
const childProcess = require('child_process')
let options = ['/c', arg1, arg2]
const myProcess = childProcess.spawn('cmd.exe', options, {
    detached: false,
    shell: false
});
let pid = myProcess.pid;

checkRunning(pid) {
  // returns true if process following pid is running
    try {
        return process.kill(pid, 0); // As a special case, a signal of 0 can be used to test for the existence of a process.
    } catch (error) {
        console.error(error);
        return error.code === 'EPERM';
    }
}
```

# READLINE

## promises

```js
import * as process from 'process'
import * as readline from 'readline/promises'

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
})

if (!existsSync(path)) {
	// create dir if it doesn't exist:
	const answer = await rl.question(`Create path: "${path}"? (yes)`)

	if (answer === 'yes' || answer === '') {
		mkdirSync(path, { recursive: true })
		console.log(`destination directory created: ${path}`)
	}
}
```

## createInterface()

```js
const readlinePromises = require('node:readline/promises');
const rl = readlinePromises.createInterface({
  input: process.stdin,
  output: process.stdout,
}); 
```

## question()

```js
import * as readline from 'node:readline/promises'
import { stdin as input, stdout as output } from 'node:process'

const rl = readline.createInterface({ input, output })
const answer = await rl.question('What do you think of Node.js? ')
console.log(`Thank you for your valuable feedback: ${answer}`)
rl.close()
```

## callback

```js
import * as readline from 'node:readline'
import * as process from 'node:process'

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

rl.question(`What's your name?`, name => {
  console.log(`Hi ${name}!`);
  readline.close();
});
```

## events

* The `'line'` event is emitted whenever the `input` stream receives an end-of-line input (`\n`, `\r`, or `\r\n`). This usually occurs when the user presses Enter or Return.
* The `'line'` event is also emitted if new data has been read from a stream and that stream ends without a final end-of-line marker.
* The listener function is called with a string containing the single line of received input.

```js
rl.on('line', (input) => {
  console.log(`Received: ${input}`);
}); 
```

SCHEDULING
=======================

## setTimout()

* returns a `Timeout` object

```js
let timerId = setTimeout(func, delay, args) // allows us to run a function once after the interval of time.
// func # function to execute
// args # arguments for the function
```

```js
// Without arguments:
function sayHi() {
  alert('Hello');
}
setTimeout(sayHi, 1000);

// With arguments:
function sayHi(phrase, who) {
  alert( phrase + ', ' + who );
}
setTimeout(sayHi, 1000, "Hello", "John"); // Hello, John
```

## clearTimout()

* accepts a Timeout object as returned by setTimeout() or 
* accepts the primitive of the Timeout object as returned by `timer[Symbol.toPrimitive]()`

```js
let timer = setTimeout(() => {
		updateDoc('users', req.user.id, { isActive: false })
		console.log(`user ${req.user.lastName} is now inactive`)
	}, time)

let timeoutID = timer[Symbol.toPrimitive]()
clearTimeout(timeoutID)
```

## setInterval()

```js
let timerId = setInterval(func, [delay], args)
// runs the function not only once, but regularly after the given interval of time.
// to stop further calls, we should call `clearInterval(timerId)`
```

```js
// repeat with the interval of 2 seconds
let timerId = setInterval(() => alert('tick'), 2000);

// after 5 seconds stop
setTimeout(() => { clearInterval(timerId); alert('stop'); }, 5000);
```

# STREAM

## solutions

## createWriteStream()

```js
/* createWriteStream */
fs.createWriteStream(destPath).write(buffer|typedArray) // returns instance of fs.WriteStream

const readable = Readable.from(ssrResult) // creates readable streams out of *iterators*
const writeStream = createWriteStream(filepath)
readable.pipe(writeStream)
```

```js
const writeStream = fs.createWriteStream("append.txt", { flags:'a' });

[...Array(10000)].forEach( function (item,index) {
    writeStream.write(index + "\n");
})

stream.end()
```

### streamToString()

```js
async function streamToString(stream) {
    // lets have a ReadableStream as a stream variable
    const chunks = [];

    for await (const chunk of stream) {
        chunks.push(Buffer.from(chunk));
    }

    return Buffer.concat(chunks).toString("utf-8");
}

// usage:
const readable = Readable.from(ssrResult)
let ssrResultString = await streamToString(readable)
console.log(ssrResultString)
```

## Readable.from()

A utility method for creating readable streams out of *iterators*

Returns: `<stream.Readable>`

```js
const { Readable } = require('stream')
import { Readable } from 'stream'

async function * generate() {
  yield 'hello';
  yield 'streams';
}

const readable = Readable.from(generate())

readable.on('data', (chunk) => {
  console.log(chunk)
})
```

```js
const readable = Readable.from(ssrResult)
const writeStream = createWriteStream(filepath)
readable.pipe(writeStream)
```

# UTILS

# JSON

### reading

```js
// require
const myJson = require('./myJson.json')
// es6 import
import myJson from './example.json' assert {type: 'json'}

// if myJson is an array of objects:
myJson.forEach(item => {
    console.log(`${item.name}: ${item.type}`)
})
```

Downsides of using the this method:

* It only reads the file once and cache data; requiring it again with simply return the cached data.
* The file must have the .json extension. Without the proper extension, the require() method won't treat it as a JSON file.
  Because of the above limitations, require() is only suitable for loading static configuration files that don't change often. For reading a dynamic file like databases.json, you should use the fs.readFile() method instead.

### writing

```js
const fs = require('fs');
let user = {
    name: 'John Doe',
    email: 'john.doe@example.com',
    age: 27,
    gender: 'Male',
    profession: 'Software Developer'
}
// convert JSON object to a string
const data = JSON.stringify(user)

// write file to disk
fs.writeFile('./user.json', data, 'utf8', (err) => {
    if (err) {
        console.log(`Error writing file: ${err}`)
    } else {
        console.log(`File is written successfully!`)
    }
})

// Flavio Copes

const fs = require('fs')

function storeData(data, path) {
  	try {
    	fs.writeFileSync(path, JSON.stringify(data))
  	} catch (err) {
    	console.error(err)
	}
}
```

### updating

```js
const fs = require('fs');
// read the file
fs.readFile('./users.json', 'utf8', (err, data) => {

  if (err) {
    console.log(`Error reading file from disk: ${err}`);
  } else {
    // parse JSON string to JSON object
    const users = JSON.parse(data);

    // add a new record
    users.push({
      name: 'Frida Kalo',
      age: 39
    });

    // write new data back to the file
    fs.writeFile('./users.json', JSON.stringify(users, null, 4), (err) => {
      if (err) {
        console.log(`Error writing file: ${err}`);
      }
    });
  }
});
```

