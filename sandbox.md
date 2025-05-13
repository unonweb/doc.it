# LINKS

- https://hanako.codeberg.page/
- What is browser sandboxing? How to escape the sandbox?
  https://misile00.github.io/notes/Browser-Sandboxing

# TYPES

## container/wrapper sandbox

A container sandbox builds an isolated process environment and then executes the target application inside. This kind of sandbox **will require permissions for all actions the target application process executes during its entire lifetime**, from initializing the configuration to rendering user supplied data (e.g. files, webpages). If the application at some point during its runtime requires access to a resource, it will keep this access indefinitely, even if access is only required during the initialization phase of the application. 

How effective this kind of sandbox is, depends a lot on the required permissions, but in general it cannot nearly reach the level of process isolation that other types of sandboxes can achieve.

## native sandbox

- file
- zathura

Unlike with container/wrapper sandboxes, these sandbox implementations **can set additional restrictions during their lifetime**.

For example, zathura will setup a GUI, load its configuration files unrestricted (which is harmless anyway) and before the dangerous part of parsing the target document is executed, it loads a seccomp filter that prevents the majority of system calls from being executed. By the time the target document is loaded and a possible parsing vulnerability may lead to code execution, the process is no longer able to open new files with write permissions or access sockets. These kinds of restrictions are not possible inside a container sandbox and provide a significant advantage.
## broker sandbox

- Chromium
- Firefox

This is basically implemented through a multi process software architecture, where a broker process will be used to provide any requested resources to the sandboxed child processes, which do not have any access permissions themselves. Any time the child process needs access to a resource, it will have to request it from the broker, which will verify if the request is safe/valid and then provide it to the child process.

If every application had a sandbox environment like chromium/firefox, desktop application security would be on a whole different level and could actually compare to app isolation that is already established on iOS and Android.

# LIMITS

The problem with this is that Exim has a single process model which means that the process doing local delivery can be a child of the process that initially received the message. So the main mon process needs all the access for delivering mail (writing to /home etc). This also means that every other child of mon will get such access including programs that receive untrusted data from the Internet. Most of the extra access needed by Exim is not a problem, but /home access is a potential risk. It also means that more effort is needed when reviewing the access control.