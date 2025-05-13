# LINKS

- https://www.cvedetails.com/product/50646/Microsoft-Visual-Studio-Code.html?vendor_id=26
# Find & Replace

```sh
# regex:
/^\s+|\s+$|\s+(?=\s)/g

# remove empty lines
^\s*$\n
```

* ^ - matches the beginning of the line
* \s - matches any white space character
* * quantity operator for \s = match zero or more
* $ - matches end of the line
* \n - matches new line character

# Paths

Every user should have its own settings folder (*~/.config/Code/User*) and extension folder (*~/.vscode/extensions*). In order to sync settings and extensions between users simple copy/paste should do or some sync extension can be used.

```sh
~/.config/Code/User
~/.vscode/extensions
```

# SSH

* https://medium.com/@krisztian.sala/vs-code-remote-ssh-as-another-user-57d449e20fb1

Keyboard shortcuts
=============================================


`Ctrl + Backtick`   # open terminal
`Ctrl + Space`      # Trigger Suggest
`Ctrl + Shift+B`    # Run Build Task
`Ctrl + +`          # zoom in
`Ctrl + -`          # zoom out

### Markdown

`Ctrl+Shift+V`  # switch between views
`Ctrl+K V`      # side-by-side preview

# Config

```json
// toggle inlay hints
"editor.inlayHints.enabled": "offUnlessPressed",
```

## launch.json

```json
{
	// Use IntelliSense to learn about possible attributes.
	// Hover to view descriptions of existing attributes.
	// For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
	"version": "0.2.0",
	"configurations": [
		{
			"type": "firefox",
			"request": "launch",
			"reAttach": true,
			"name": "Launch Firefox",
			"url": "http://localhost:8000/comp_img/index.html",
			"webRoot": "${workspaceFolder}",
			
      // https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_skipping-uninteresting-code
      "skipFiles": [
				"${workspaceFolder}/node_modules/**/*.js",
        "<node_internals>/**/*.js"
			]
		},
	]
}
```

# Snippets

* https://code.visualstudio.com/docs/editor/userdefinedsnippets

```js
{
    "key": "shift+alt+y",            // or whatever keybinding you want
    "command":  "editor.action.insertSnippet",
    "args": {
      "snippet": "${TM_SELECTED_TEXT/\\s//g}"  // replace spaces with nothing
    },
    "when": "editorTextFocus && editorHasSelection"
},
```

Built in Extensions
=============================================


### Markdown

// Place your settings in this file to overwrite default and user settings.
```js
    {
    "markdown.styles": ["Style.css"]
    }
```

#### change the look of the Markdown preview with CSS

Stylesheets are registered using the `markdown.previewStyles` Contribution Point in the extension's `package.json`

```js
    "contributes": {
        "markdown.previewStyles": [
            "./style.css"
        ]
    }
```

#### add support for new syntax with markdown-it plugins

To contribute a markdown-it plugin, first add a "markdown.markdownItPlugins" contribution in your extension's package.json:

```js
    "contributes": {
        "markdown.markdownItPlugins": true
    }
```
Then, in the extension's main activation function, return an object with a function named extendMarkdownIt. This function takes the current markdown-it instance and must return a new markdown-it instance:
```js
    import * as vscode from 'vscode';

    export function activate(context: vscode.ExtensionContext) {
    return {
        extendMarkdownIt(md: any) {
        return md.use(require('markdown-it-emoji'));
        }
    };
    }
```

#### compile Markdown into HTML

1. Install a Markdown compiler: `npm install -g markdown-it`
2. Create tasks.json:  
    `Terminal > Configure Tasks... > Create tasks.json file from templates`  
    VS Code then presents a list of possible tasks.json templates to choose from. Select *Others* since we want to run an external command.  
    This generates a tasks.json file in your workspace .vscode folder

To use markdown-it to compile the Markdown file, change the contents as follows:
```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Compile Markdown",
            "type": "shell",
            "command": "markdown-it sample.md -o sample.html",
            "group": "build"
        }
    ]
}
```
If you want to make the *Compile Markdown* task the default build task to run execute *Configure Default Build Task* from the global Terminal menu and select *Compile Markdown* from the presented list. The final tasks.json file will then look like this:
```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Compile Markdown",
            "type": "shell",
            "command": "markdown-it sample.md -o sample.html",
            "problemMatcher": [],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}
```
