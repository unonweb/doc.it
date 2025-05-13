# LINKS

- List of software using Electron:
  https://en.wikipedia.org/wiki/List_of_software_using_Electron
- - https://www.electronjs.org/apps
# ELECTRON

Electron consists of three main pillars:

* **Chromium** for displaying web content.
* **Node.js** for working with the local filesystem and the operating system.
* **Custom APIs** for working with OS native functions.
# CLI

- https://www.electronjs.org/docs/latest/api/command-line-switches

```sh
--no-sandbox # Disables the Chromium sandbox
--remote-debugging-port=port # Enables remote debugging over HTTP on the specified port.
```

# DEBUG / INSPECT

1. Launch your Electron application with `--remote-debugging-port=8315`
2. go to chrome://inspect/#devices
3. click `Configure...` and add `localhost:8315` as a discovery server
# SANDBOX

In Chromium, sandboxing is applied to most processes other than the main process. This includes renderer processes, as well as utility processes such as the audio service, the GPU service and the network service.

Starting from Electron 20, the sandbox is enabled for renderer processes without any further configuration.

## no sandbox

- https://no-sandbox.io/



# PROCESSES

#### main process
* **creates** web pages by creating BrowserWindow instances. Each BrowserWindow instance runs the web page in its Renderer process. When a BrowserWindow instance is destroyed, the corresponding Renderer process gets terminated as well.
* **manages** all web pages and their corresponding Renderer processes.

#### renderer process
* manages only the corresponding web page. A crash in one Renderer process does not affect other Renderer processes.
* communicates with the Main process via IPC to perform GUI operations in a web page. Calling native GUI-related APIs from the Renderer process directly is restricted due to security concerns and potential resource leakage.

The communication between processes is possible via Inter-Process Communication (IPC) modules: **ipcMain** and **ipcRenderer**.
