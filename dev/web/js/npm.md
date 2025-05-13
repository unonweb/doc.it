# INFO

* https://docs.npmjs.com/cli/v10/configuring-npm/npmrc

# RESOLUTION

1. **Current `node_modules` folder.** (local)

Additionally, Node.js will search in the following list of GLOBAL_FOLDERS:

- 1: `$HOME/.node_modules`
- 2: `$HOME/.node_libraries`
- 3: `$PREFIX/lib/node`

## require global modules

`require` doesn't look in the folder where global modules are installed. You can fix this by setting the NODE_PATH environment variable. 

```sh
# ~/.profile
export NODE_PATH=$(npm root -g)
```

# SCOPES

When you sign up for an npm user account or create an Org, you are granted a scope that matches your user or Org name. You can use this scope as *a namespace for related packages*.

A scope allows you to create a package with the same name as a package created by another user or Org without conflict.  

When listed as a dependent in a package.json file, scoped packages are preceded by their scope name. The scope name is everything between the @ and the slash:  

# MODULES

A module is any file or directory in the node_modules directory that can be loaded by the Node.js require() function.

```js
let req = require('request')
```

To be loaded by the Node.js `require()` function, a module must be one of the following:

* A folder with a package.json file containing a "main" field.
* A folder with an index.js file in it.
* A JavaScript file.

# package.json

```js
"browser":
/* If your module is meant to be used client-side the browser field should be used instead of the main field. This is helpful to hint users that it might rely on primitives that aren't available in Node.js modules. (e.g. window) */

"main": "src/main.js", 
// a module ID that is the primary entry point to your program
// if your package is named foo, and a user installs it, 
// and then does require("foo"), then your main module's exports object will be returned.
// should be a module relative to the root of your package folder
// if not set it defaults to index.js in the packages root folder
    
"dependencies": {
    "vue": "^2.5.2", // compatible with version 2.5.2
    "vue": ">=3.0", // must be greater than 3.0
    "vue" : "3.3.x", // 3.3.1, 3.3.2, etc., but not 3.4.1
    "vue" : "latest",
},
"devDependencies": {
    "css-loader": "^0.28.0",
},
```

## as a build-tool

* https://deliciousbrains.com/npm-build-script/

```json
"scripts": {
    "scss": "sass assets/scss/style.scss dist/css/style.css",
    "concat:css": "concat -o dist/css/styles.css dist/css/style.css assets/css/test.css",
    "concat:js": "mkdir -p dist/js && concat -o dist/js/scripts.js assets/js/test1.js assets/js/test2.js",
    "concat": "npm run concat:css && npm run concat:js",
    "cssmin": "cleancss -o dist/css/styles.min.css dist/css/styles.css",
    "uglify": "uglifyjs -o dist/js/scripts.min.js dist/js/scripts.js",
    "squoosh": "squoosh-cli --mozjpeg '{quality:70}' assets/img -d dist/img",
    "build:css": "npm run scss && npm run concat:css && npm run cssmin",
    "build:js": "npm run concat:js && npm run uglify",
    "build:img": "npm run squoosh",
    "build": "npm run build:css && npm run build:js && npm run build:img",
    "watch:css": "onchange 'assets/scss/*.scss' -- npm run build:css",
    "watch:js": "onchange 'assets/js/*.js' -- npm run build:js",
    "watch": "npm-run-all --parallel watch:*"
},
```

# NPM

## cli

### root

```sh
npm root -g # /home/frida/code/web/node_modules_global/lib/node_modules
```

### install

```shell
npm install # (in package directory, no arguments):
# installs all modules listed as dependencies in package.json in the local node_modules folder

npm install <pkg>@4.9.1 # install the exact version of a package    
npm install <pkg>@^4.0.0 # install the latest 4.x.x version   
npm install # without naming a package to install, NPM will install the dependencies listed in *package.json*.

# saves any specified packages into dependencies by default.

# Additionally, you can control where and how they get saved with some additional flags:
npm install --save-dev # Package will appear in your devDependencies   
npm install --save-optional # Package will appear in your optionalDependencies   
npm install --no-save # Prevents saving to dependencies   
```

### global & local

* Install it locally if you're going to `require()` it.
* Install it globally if you're going to run it on the command line.
* If you need both, then install it in both places, or use npm link

### global

* packages are install in: 	*prefix/lib/node_modules* 
* bins are installed in:          *prefix/bin*

```shell
npm install -g # install globally
npm config get prefix # show prefix path
```

### local

```sh
ll	*./node_modules* # packages
ll *./node_modules/.bin* # bins
```

Local mode is the default.

npm looks for an existing folder called *node_modules* in the current directory and creates a folder for each package you install in that folder. If it can't find an existing node_modules folder here, it then looks through the current directory's ancestors until it finds one. If it can't find one, it creates one in the current directory.

### uninstall, prune

```shell
npm uninstall # It also removes the package from the dependencies, devDependencies, etc
npm uninstall # takes 3 exclusive, optional flags which save or update the package version in your main package.json: 

npm uninstall -s | --save # Package will be removed from your dependencies
npm uninstall -D | --save-dev # Package will be removed from your devDependencies
npm uninstall -O | --save-optional # Package will be removed from your optionalDependencies
npm uninstall --no-save # Package will not be removed from your package.json file

npm prune # removes all packages present in the node_modules folder that are not listed as any package's dependency list.
# if the --production flag is specified or the NODE_ENV environment variable is set to production, this command will remove the packages specified in your devDependencies
npm prune --dry-run # no changes will actually be made.
```

### init / create


```shell
# set up a new or existing npm package
# creates a package.json file
# DOES NOT INSTALL PACKAGES

npm init # will create a package.json file (legacy behaviour)
npm create # alias

npm init <initializer> 
npm create <initializer> 
npm create vite@latest # in this case is an npm package named create-vite, which will be installed by npm-exec, and then have its main bin executed -- presumably creating or updating package.json and running any other initialization-related operations.
npm exec create-vite@latest
```

### run scripts

```shell
# runs an arbitrary command from a package's "scripts" object. 
# If no "command" is provided, it will list the available scripts.
npm run-script <stage> # long
npm run <stage> # short
npm run dev # runs the script with the 'dev' key specified in package.json
```

### help


```shell
npm -l  
npm help npm
npm help install
npm help folders  
```

### list & update

```shell
npm list --global | grep <module> #
npm list -g --depth=0 #

npm list --depth 3 | grep slate # looks for 'slate' among locally installed modules

# installed versions:
npm version # lists the installed versions

# outdated packages:
npm -g outdated
npm install <pkg>@latest # install the latest version
npm -g update # If no package name is specified, all packages in the specified location (global or local) will be updated.
```

### search & view

```shell
npm search <pkg>
npm view <pkg> # aliases: v, info, show  
```

## config

### files

The four relevant files are:

```sh
/path/to/my/project/.npmrc # per-project config file
~/.npmrc # per-user config file
$PREFIX/etc/npmrc # global config file
```

#### format

All npm config files are an ini-formatted list of `key = value` parameters. Environment variables can be replaced using `${VARIABLE_NAME}`. For example:

```bash
prefix = ${HOME}/.npm-packages
```

### directives

* https://docs.npmjs.com/cli/v10/using-npm/config

```bash
prefix = <path> # Default: In global mode, the folder where the node executable is installed. Otherwise, the nearest parent folder containing either a package.json file or a node_modules folder. The location to install global items. If set on the command line, then it forces non-global commands to run in the specified folder.
```

### via cli

```shell
npm config
npm config list
npm config set
npm config set prefix <path> # set path to global modules folder
npm config get prefix
```

```js
npm config set prefix /home/frida/code/web/node_modules_global
npm -g install @web/dev-server
```

# VERSIONING

## semantic versioning

encodes some information about which versions are compatible (don’t break the old interface) in the version number

`2.3.0.` 

* Every time new functionality is added, the middle number has to be incremented.
* Every time compatibility is broken, so that existing code that uses the package might not work with the new version, the first number has to be incremented.

#### package.json

**`^`** in front of the version number for a dependency in package.json **indicates that any version compatible with the given number may be installed**. 

`^2.3.0` means that any version greater than or equal to 2.3.0 and less than 3.0.0 is allowed

