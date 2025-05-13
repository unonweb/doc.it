## @web/dev-server

```shell
# Install Web Dev Server:
npm i --save-dev @web/dev-server
npm i -g @web/dev-server

# By adding the following to the "scripts" section in package.json
# you can use 'npm start'
"start": "web-dev-server --node-resolve --open"

# Start the server:
web-dev-server # long
wds # short

wds --node-resolve # resolve bare module imports
wds --open # Opens the browser on app-index, root dir or a custom path
npx wds --open /demo/
wds --watch # Run in watch mode, reloading on file changes
wds --root-dir <path> # Sets the root directory to serve files from. Defaults to the current working directory


npx web-dev-server --help # show help
```

```json
"scripts": {
  "serve:dev": "npx -g wds --port 3000 --root-dir src/",
  "serve:src": "npm run serve:dev",
  "serve:dist": "npx -g wds --port 3001 --root-dir dist/",
},
```