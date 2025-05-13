# BASICS

### output format

* **iife**: Immediately Invoked Function Expression (function () { ... }());
* **es**: standard ES6
* **cjs**: CommonJS for Node.js
* **umd**: Universal Module Definition
* **amd**: Asynchronous Module Definition
* **system**: SystemJS modules

If in doubt, use **es**. 
**iife** may be practical if you need to support older browsers which do not support modules

### treeshaking

Be aware that Rollup.js cannot tree-shake Lodash because it exports a single object with multiple methods. Most  JavaScript libraries use a similar structure but the situation should  improve as more developers adopt ES6 modules.

# PLUGINS

use npm to install plugins locally or globally then reference them in your Rollup. js configuration file

### terser

```sh
# bash
npm install rollup-plugin-terser --save-dev
```

```js
// rollup.config.js
import { terser } from 'rollup-plugin-terser';

export default [
	{
		input: './src/main.js',

		output: {
			file: './build/bundle.js',
			format: 'es',
			sourcemap: devMode ? 'inline' : false,
			plugins: [
				terser({
					ecma: 2020, 
					mangle: { toplevel: true },
					compress: {
						module: true,
						toplevel: true,
						unsafe_arrows: true,
						drop_console: !devMode,
						drop_debugger: !devMode
					},
					output: { quote_style: 1 }
				})
			]

		}
	}
]
```

### importing modules

JavaScript libraries are often packaged as *CommonJS* modules which you can install with npm. 
Rollup.js can parse CommonJS with the following plugins:

#### node-resolve

locates a module in the project’s node_modules directory

```sh
npm install @rollup/plugin-node-resolve --save-dev
```

#### plugin-commonjs

converts CommonJS to ES6 modules.

```sh
npm install @rollup/plugin-commonjs --save-dev
```

#### rollup.config.js

```js
// rollup.config.js
import { nodeResolve } from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';

//const devMode = (process.env.NODE_ENV === 'development');
//console.log(`${devMode ? 'development' : 'production'} mode bundle`);

export default [

	{
		input: './src/main.js',
		plugins: [
			nodeResolve(),
			commonjs()
		],
		output: {
			file: './build/bundle.js',
			format: 'es'
		}
	}
];
```



# CLI

```sh
npx rollup ./src/main.js --file ./build/bundle.js --format es

npx rollup --config <filename> # Use this config file (defaults to rollup.config.js)
npx rollup --format # amd, cjs, es, iife, umd, system
npx rollup --file <path> # Single output file
npx rollup --watch # watch files in bundle and rebuild on changes
```

## package.json

```json
"scripts": {
  "rollup:help": "rollup --help",
  "rollup:es": "rollup ./src/main.js --file ./build/bundle.js --format es"
},
```

```sh
npm run rollup:es
```

# CONFIG

```js
// rollup.config.js

export default [
	{
		input: './src/main.js',
		output: {
			file: './build/bundle.js',
			format: 'es'
		}
	}
];
```





