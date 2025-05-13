# Troubleshooting

## exclude server only modules

```sh
rm -r node_modules/.cache/
```

```js
// fallback
webpack.resolve.fallback = {
  ...config.resolve.fallback,
  path: false,
  fs: false,
  child_process: false,
  util: false,
  worker_threads: false,
  esbuildPaths: false,
  tty: false,
  promisify: false,
  assert: false,
  os: false,
  '/home/payload/cms/src/helpers/esbuildPaths.js': false, // does not work!
}
// alias
webpack.resolve.alias = {
  ...config.resolve.alias,
  '/home/payload/cms/src/helpers/esbuildPaths.js': mockModulePath, // works!
}
```

