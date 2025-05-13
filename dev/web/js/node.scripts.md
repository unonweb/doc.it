# LINKS

* **Das Ende von Bash**: https://www.heise.de/hintergrund/TypeScript-erobert-die-Kommandozeile-Das-Ende-von-Bash-9980116.html?seite=3
* Command Line Interface Guidelines: https://clig.dev/
* Node.js CLI Apps best practices: https://github.com/lirantal/nodejs-cli-apps-best-practices

# TOOLS

* commander-js: https://github.com/tj/commander.js/
  command-line interfaces made easy 
* prompts: https://www.npmjs.com/package/prompts
* fuzzy finder: https://github.com/junegunn/fzf
* pgk: https://www.npmjs.com/package/pkg

# ARGS
## pass

```bash
node myapp.js arg # standalone
node myapp.js arg=value # key=value
```

## retrieve

Use the *process object* built into node. It exposes an *argv property*, which is an array that contains all the command line invocation arguments: 

```js
process.argv // an array of strings
// also includes the name of the *node* command and your script name, so the actual arguments start at index 2

process.argv[0] // path of the node executable
process.argv[1] // path of the file being executed
process.argv[2] // first argument passed
process.argv[3] // second argument passed

// Get only the additional arguments 
// by creating a new array that excludes the first 2 elements:
const args = process.argv.slice(2)
```

### flags

```js
// Check to see if the -f argument is present
const flag = (  
	process.argv.indexOf('-f') > -1 ? 'Flag is present.' : 'Flag is not present.'
);
```

```js
if (process.argv[2] && process.argv[2] === '-f') {
  console.log('Flag is present.');
} 
else {
  console.log('Flag is not present.');
}
```

### values

```javascript
// Checks for --custom and if it has a value
const customIndex = process.argv.indexOf('--custom')
let customValue

if (customIndex > -1) {
  // Retrieve the value after --custom
  customValue = process.argv[customIndex + 1];
}

const custom = (customValue || 'Default');

console.log('Flag:', `${flag}`);
console.log('Custom:', `${custom}`);
```

```js
function getArgValue(argName = '', defaultValue = '') {
  /* 
    ATT: 
      Does not parse parameters given like --config=/path/to/file
      Only parses: --config /path/to/file
    NOTE:
      Extracts node cli arguments by name
    USE:
      getArgValue('--config') 
  */

  // Checks for argName and if it has a value
  let argIndex = process.argv.indexOf(argName)
    
  if (argIndex > -1) {
    // Retrieve the value after argName
    return process.argv[argIndex + 1];
  }
  else {
    return defaultValue ?? undefined
  }
}
```
### iterate over args

iterate over all the arguments (including the node path and the file path) using a loop:

```js
process.argv.forEach((val, index) => {
	console.log(`${index}: ${val}`)
})
```
