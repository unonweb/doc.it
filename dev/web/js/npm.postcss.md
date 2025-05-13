# postcss

- https://github.com/postcss/postcss

## cli

* https://www.npmjs.com/package/postcss-cli#usage

```sh
npm i -g postcss postcss-cli

# Basic options:
postcss --output # Output file; postcss -o 
postcss --dir # Output directory; postcss -d
postcss --replace # Replace (overwrite) the input file; postcss -r
postcss --map #  Create an external sourcemap; postcss -m
postcss --no-map #  Disable the default inline sourcemaps
postcss --watch # Watch files for changes and recompile as needed; postcss -w
postcss --verbose # Be verbose
postcss --env #  A shortcut for setting NODE_ENV

postcss --config # Set a custom directory to look for a config file  [string]

# Options for use without a config file:
postcss input.css --output output.css
```

```sh
postcss --use autoprefixer -o main.css css/*.css # run Autoprefixer through all the files contained in the /css/ folder and output the transformed CSS into main.css.
```

## config

* https://github.com/michael-ciniawsky/postcss-load-config
* https://github.com/csstools/postcss-plugins/tree/main/plugin-packs/postcss-preset-env

If you need to pass options to your plugins, or have a long plugin chain, you'll want to use a configuration file.

Note that you **can not** set the `from` or `to` options for postcss in the config file. They are set automatically based on the CLI arguments.

```sh
nano postcss.config.js # in the root of your project
```

```js
// postcss.config.js
module.exports = {
  plugins: [
    require('postcss-preset-env')({ ...options }),
  ],
}
```

## use in js

```js
try {
	let css = await fs.readFile(srcPath)
	postcss([
		postcssPresetEnv(presetEnvOptions),
	]).process(css, { from: srcPath, to: destPath })
		.then(result => {

			fs.writeFile(destPath, result.css, () => true)
			console.log('written: ', destPath)

			if (result.map) {
				fs.writeFile(`${destPath}.map`, result.map.toString(), () => true)
			}
		})
} catch (error) {
	console.error(error)
}
```

# Plugins

## autoprefixer

Automatically adds vendor prefixes to CSS rules using values from Can I Use.

```sh
npm i -g autoprefixer
npx postcss *.css --use autoprefixer -d build/
npx -g postcss *.css --use autoprefixer --replace --no-map --verbose
npx -g postcss site.css --use autoprefixer --replace --no-map --verbose --browsers "iOS > 15"
```

## doiuse

```sh
npm i -g doiuse
npx doiuse --browsers "ie >= 9, > 1%, last 2 versions" main.css
npx doiuse --browsers # Autoprefixer-like browser criteria; default: null
npx doiuse --ignore # List of features to ignore; default: ""
npx doiuse --list-only # Just show the browsers and features that would be tested by the specified browser criteria, without actually processing any CSS.
npx doiuse --config # Provide options through config file
npx doiuse --verbose #  Verbose output. Multiple levels available.
npx doiuse --json #  Output JSON instead of string linter-like messages.

npx -g doiuse --browsers "iOS > 15" site.css 
```

## postcss-preset-env

* github:
  https://github.com/csstools/postcss-plugins/tree/main/plugin-packs/postcss-preset-env
* npm:
  https://www.npmjs.com/package/postcss-preset-env
* Features:
  https://github.com/csstools/postcss-plugins/blob/main/plugin-packs/postcss-preset-env/FEATURES.md

Allows you to use modern, yet-to-be-finalized CSS features. It converts modern CSS into something most browsers can understand, determining the transformations and polyfills you need based on your targeted browsers or runtime environments.

A plugin that is essentially a big group of plugins designed to mimic what of “future CSS” that it can.

### cli

```sh
npm i -g postcss postcss-cli postcss-preset-env
npm i -g postcss-preset-env

# without config
npx -g postcss --use postcss-preset-env --output post.css
```

### config

#### browsers
- https://github.com/csstools/postcss-plugins/tree/main/plugin-packs/postcss-preset-env

```js
defaults // > 0.5%, last 2 versions, Firefox ESR, not dead
```

```js
options.stage // determines which CSS features to polyfill, based upon their stability in the process of becoming implemented web standards
options.stage = 2 // default
options.stage = false // disable every polyfill

options.browsers // determines which polyfills are required based upon the browsers you are supporting
options.browsers = 'defaults'

options.features // Any polyfills not explicitly enabled or disabled through `features` are determined by the `stage` option.
options.features['nesting-rules'] = true
```

```js
let presetEnvOptions = {
	stage: 2, // default
	browsers: 'defaults',
	features: {
		'nesting-rules': true
	}
}```

### PostCSS Nested
follows SASS
 
#### PostCSS Nesting
follows the official spec

- https://github.com/csstools/postcss-plugins/tree/main/plugins/postcss-nesting





## cssnano

Reduces the size of your CSS file

## stylelint