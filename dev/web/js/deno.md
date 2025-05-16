
# CLI

- https://docs.deno.com/runtime/reference/cli/

```sh
--allow-read="." # relative to the entry script
--allow-read=$(which deno)
```

# deno.json

- https://docs.deno.com/runtime/fundamentals/configuration/

## tasks

- https://docs.deno.com/runtime/fundamentals/configuration/#tasks

# DEBUGGING

- https://docs.deno.com/runtime/fundamentals/debugging/

```sh
deno run --inspect main.ts # executes with an inspector server which allows client connections from tools that support the V8 Inspector Protocol (chrome://inspect)


deno run --inspect-wait main.ts # wait for a debugger to connect before executing
```

## launch.json

```json
"program": "${workspaceFolder}/main/main.js",
// Executable or file to run when launching the debugger.
"program": "${fileBasename}" 
// means: “The current opened file's basename with no file extension”.
```
# ENV

- https://docs.deno.com/runtime/reference/env_variables/

```sh
deno run --env-file <script> # reads .env from the current working directory or the first parent directory that contains one
```

```ts
Deno.env.set("FIREBASE_API_KEY", "examplekey123");

Deno.env.get("FIREBASE_API_KEY"); // examplekey123
Deno.env.has("FIREBASE_API_KEY"); // true

Deno.env.toObject()
```

# ERRORS

- https://medium.com/deno-the-complete-reference/unhandled-rejection-denos-equivalent-of-node-js-12d2fce1418
- https://medium.com/deno-the-complete-reference/deno-nuggets-handle-uncaught-errors-566a2d91f379

# MODULES

- https://docs.deno.com/runtime/fundamentals/modules/

## standard library

- https://docs.deno.com/runtime/fundamentals/standard_library/

```sh
deno add jsr:@std/fs jsr:@std/path
```

## third party modules

```js
import { camelCase } from "jsr:@luca/cases@1.0.0";
import { say } from "npm:cowsay@1.6.0";
import { pascalCase } from "https://deno.land/x/case/mod.ts";
```

### centralize imports

**deno.js**
```json
{
  "imports": {
    "@std/assert": "jsr:@std/assert@^1.0.0",
    "chalk": "npm:chalk@5"
  }
}
```

Then your script can use the bare specifier `std/assert`:

**script.js**
```js
import { assertEquals } from "@std/assert";
import chalk from "chalk";

assertEquals(1, 2);
console.log(chalk.yellow("Hello world"));
```

## jsr

- https://jsr.io/@std

# STYLE

- https://docs.deno.com/runtime/fundamentals/linting_and_formatting/

# VSCODE

- https://docs.deno.com/runtime/reference/vscode/