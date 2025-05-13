# INSTALL

```sh
npm -g install parcel
```

# CLI
- parameters:
  https://parceljs.org/features/cli/#parameters

```sh
npx parcel src/index.html #
npx parcel serve src/index.html # the same
npx parcel src/index.html --https #
npx parcel src/index.html --cert certificate.cert --key private.key

npx parcel watch src/index.html
# The watch command is similar to serve, but does not start a dev server. 
# However, it automatically rebuilds your app as you make changes, and supports hot reloading. 
# Use watch if you're building a library, a backend, or have your own dev server.

npx parcel build src/index.html
# performs a single production build and exits.
# enables scope hoisting and other production optimizations by default.
npx parcel build --no-optimize # disable minification
npx parcel build --no-source-maps # disable sourcemaps
npx parcel build --no-cache # disable the filesystem cache
npx parcel build --config <path> # specify which config to use
npx parcel build --dist-dir <dir> #output directory to write to when unspecified by targets
```

# Targets

- https://parceljs.org/features/targets/#targets

Parcel follows the dependencies in each resolved entry to build your source code for one or more targets. Targets specify the output directory or file path, as well as information about how your code should be compiled.

```json
{
  "modern": "dist/modern.js",
  "legacy": "dist/legacy.js",
  "targets": {
    "modern": {
      "engines": {
        "browsers": "Chrome 80"
      }
    },
    "legacy": {
      "engines": {
        "browsers": "> 0.5%, last 2 versions, not dead"
      }
    }
  }
}
```


# CSS
## Transpilation

- https://parceljs.org/languages/css/#transpilation

### Default plugins

Parcel includes equivalents of `autoprefixer` and `postcss-preset-env` automatically when a `browserslist` is specified in your `package.json`. These are implemented in Rust and are significantly faster than PostCSS. If these are the only transforms you need in your project, then you may not need PostCSS at all.

### Browser targets

By default Parcel does not perform any transpilation of CSS syntax for older browsers. This means that if you write your code using modern syntax or without vendor prefixes, that’s what Parcel will output. You can declare your app’s supported browsers using the `browserslist` field in your package.json. When this field is declared, Parcel will transpile your code accordingly to ensure compatibility with your supported browsers.

```json
{  "browserslist": "> 0.5%, last 2 versions, not dead"}
```

### Vendor prefixing

Based on your configured browser targets, Parcel automatically adds vendor prefixed fallbacks for many CSS features.

### CSS nesting

- https://parceljs.org/languages/css/#nesting

Parcel compiles this syntax to un-nested style rules that are supported in all browsers today.

Because nesting is a draft, it is not enabled by default. To use it, enable it by configuring `@parcel/transformer-css` in your project root `package.json` file.

```json
{  "@parcel/transformer-css": { 
  "drafts": {
    "nesting": true
    }
  }
}
```

Once enabled, any CSS file in your project can use directly nested style rules or the `@nest` at rule.

### include postCSS

either:

```js
// postcss.config.js
const config = {
  plugins: [
    //require('autoprefixer'),
    require('postcss-nesting')
  ]
}
module.exports = config
```

or:

```json
// .postcssrc
{
  "plugins": {
    "postcss-nesting": {}
  }
}
```
# CONFIG

```json
{
	"source": "./index.html",
	"scripts": {
		"start": "parcel",
		"build": "parcel build"
	},
    "targets": {
    	"default": {
      		"distDir": "./dist",
            "optimize": true,
            "context": "browser",
            "outputFormat": "esmodule",
            "isLibrary": false,
            "includeNodeModules": true,
            "sourceMap": false,
    	}
  	}
}
```

* **"context"**:

   'node' | 'browser' | 'web-worker' | 'service-worker' | 'worklet' | 'electron-main' | 'electron-renderer'

  defines what type of environment to build for. This tells Parcel what environment-specific APIs are available, e.g. the DOM, Node filesystem APIs, etc.

* **"outputFormat"**: 

  'global' # a classic script that could be loaded in a `<script>` tag in the browser. Not supported for library targets.

-  'esmodule' # ES module using import and export statements. Could be loaded in a `<script type="module">` tag in the browser, or loaded by Node.js or another bundler.

- 'commonjs' # CommonJS module using require and module.exports. Could be loaded by Node.js or another bundler.

```js
"sourceMap": "inline" 
// Whether to inline the source map as a data URL into the bundle rather than link to it as a separate output file.
"sourceMap": "inlineSources" 
// Whether to inline the original source code into the source map rather than load them from the sourceRoot. This is set to true by default when building browser targets for production.
"sourceMap": "sourceRoot" 
// The URL at which to load the original source code. This is set automatically in development when using the builtin Parcel dev server. Otherwise, it defaults to a relative path to the bundle from the project root.

"sideEffects": false // All files in this package have no side effects.
"sideEffects": "string" // A glob matching files that includes side effects.
"sideEffects": "Array<string>" // An array of globs matching files that include side effects.

/* When a file is marked as side effect free, Parcel is able to skip the entire file when concatenating the bundle if it does not have any used exports. This can reduce bundle sizes significantly, especially if the module calls helper functions during its initialization. */
```
