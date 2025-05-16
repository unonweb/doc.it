- https://docs.deno.com/api/deno/~/Deno

# STD LIBRARY

## fs

- https://jsr.io/@std/fs
# DENO
## Deno.execPath()

- https://docs.deno.com/api/deno/~/Deno.execPath
- Returns the path to the current deno executable.


## subprocess

### Deno.Command()

- https://docs.deno.com/api/deno/subprocess
- https://docs.deno.com/api/deno/~/Deno.CommandOptions
- https://medium.com/deno-the-complete-reference/run-a-child-process-or-execute-shell-commands-in-deno-4c29a5be6773

```js
// define command used to create the subprocess
const command = new Deno.Command("echo", {
  args: [
    "Hello from Deno!",
  ],
});

// create subprocess and collect output
const { code, stdout, stderr } = await command.output();

console.assert(code === 0);
console.log(new TextDecoder().decode(stdout));
console.log(new TextDecoder().decode(stderr));
```

```shell
deno run --allow-run=echo ./subprocess_simple.ts
```

### ex 2

```js
// async version
const proc = await new Deno.Command("cmd", { args: ['1', '2'] }).output();  

// sync version
const proc = new Deno.Command("cmd", { args: ['1', '2'] }).outputSync();
```

```js
const proc = new Deno.Command("cmd", { args: ['1', '2'] }) 
proc.output() // Executes the Deno.Command, waiting for it to finish and collecting all of its output.
```

The output (i.e. the proc variable in the sample code) will be available when the child process finishes. The output contains:

- _success_: true or false
- _code_: the child process exit code
- _signal_: set if child process caught a signal
- _stdout_: A getter to receive the output buffer from the child process
- _stderr_: A getter to receive the error buffer from the child process

### communicating with subprocesses

By default when you use `Deno.Command()` the subprocess inherits stdin, stdout and stderr of the parent process. If you want to communicate with a started subprocess you must use the "piped" option.
### spawn

- https://docs.deno.com/examples/subprocesses_spawn/

```js
const process = new Deno.Command("cmd", { args: ['1', '2'] }) 
// If we want to interact with a process we first need to spawn it:
const process = command.spawn();

// We can now pipe the input into stdin. 
// To do this we must first get a writer from the stream and write to it
const writer = process.stdin.getWriter();
await writer.write(new TextEncoder().encode("console.log('hello')"));
writer.releaseLock();

// We must then close stdin:
await process.stdin.close();

// We can now wait for the process to output the results:
const result = await process.output();
console.log(new TextDecoder().decode(result.stdout));
```

1. `writer.ready`: This is a promise that resolves when the writer's desired size is positive, indicating that it can accept more chunks to write. In other words, it's a signal that the writer is ready to write data. By awaiting `this.writer.ready`, you're ensuring that the writer is ready before attempting to write data to it. 
2. `writer.write(data)`: This method is used to write data to the stream's internal queue. It returns a promise that resolves when the data has been handled (e.g., written to the underlying sink). 
3. `writer.releaseLock()`: This method releases the writer's lock on the corresponding stream, allowing other writers to acquire a lock and write to the stream. It's important to release the lock when you're done writing so that other parts of your code (or other code entirely) can write to the stream without being blocked. In the `stdin` method, you're correctly awaiting `writer.ready` before writing data to the stream. However, you should be cautious with `writer.releaseLock()`. Typically, you would only release the lock if you're completely done with writing and you want to allow other writers to start writing to the stream. If you plan to write more data later, you should not release the lock after each write, as it could lead to unnecessary overhead and potential race conditions with other writers.
# WEB

## TextDecoder

Represents a decoder for a specific text encoding, allowing you to convert binary data into a string given the encoding.

```js
const decoder = new TextDecoder('utf-8');
const buffer = new Uint8Array([72, 101, 108, 108, 111]);
const decodedString = decoder.decode(buffer);
console.log(decodedString); // Outputs: "Hello"
```
