# NOTES

## bundling

Note that **bundling** is different than file **concatenation**. Passing esbuild multiple input files with bundling enabled will create multiple separate bundles instead of joining the input files together. To join a set of files together with esbuild, import them all into a single entry point file and bundle just that one file with esbuild.

Each entry point file will become a separate bundle. Each bundle  includes the entry point file and all files it imports. Passing two  entry points will create two separate bundles. The bundling process is  not the same thing as file concatenation.

To **bundle** a file means to **inline any imported dependencies** into the file  itself. This process is recursive so dependencies of dependencies (and  so on) will also be inlined.

# CLI

```sh
# js
npx -g esbuild --format=esm --bundle --outfile=bundle-ce.js --platform=browser import-cs.js

# css
npx -g esbuild --format=esm --bundle --outfile=bundle-ce.css --platform=browser \ 
/srv/web/resources/custom-elements/prod/nav/nav.css \
/srv/web/resources/custom-elements/prod/lit-posts/lit-posts.css
```

```sh
npx -g esbuild --minify
# removes whitespace
# rewrites your syntax to be more compact
# renames local variables to be shorter

--external:M # Exclude module M from the bundle (can use * wildcards)
```

# JS

```js
import * as esbuild from 'esbuild'

await esbuild.build({
  entryPoints: ['in.js'],
  bundle: true,
  outfile: 'out.js',
})
```

## stdin

```js
import * as esbuild from 'esbuild'

let result = await esbuild.build({
  stdin: {
    contents: `export * from "./another-file"`,

    // These are all optional:
    resolveDir: './src',
    sourcefile: 'imaginary-file.js',
    loader: 'ts',
  },
  format: 'cjs',
  write: false,
})
```

## write

By default the CLI and JavaScript APIs write to the file system and the Go API doesn't. To use the in-memory buffers:

```js
import * as esbuild from 'esbuild'

let result = await esbuild.build({
  entryPoints: ['app.js'],
  sourcemap: 'external',
  write: false,
  outdir: 'out',
})

for (let out of result.outputFiles) {
  console.log(out.path, out.contents, out.hash, out.text)
}
```

## working directory

This API option lets you specify the working directory to use for the build. It normally defaults to the current [working directory](https://en.wikipedia.org/wiki/Working_directory) of the process you are using to call esbuild's API. The working  directory is used by esbuild for a few different things including  resolving relative paths given as API options to absolute paths and  pretty-printing absolute paths as relative paths in log messages. Here  is how to customize esbuild's working directory:

```sh
cd "/var/tmp/custom/working/directory"
```

```js
import * as esbuild from 'esbuild'

await esbuild.build({
  entryPoints: ['file.js'],
  absWorkingDir: '/var/tmp/custom/working/directory',
  outfile: 'out.js',
})
```
