# LINKS

- https://hkubota.wordpress.com/2020/12/31/comparing-sandboxing-tools/
- https://snyk.io/de/blog/what-is-a-backdoor/

When programming in Node.js, a huge problem is that “npm install” downloads libraries you did not specify. It downloaded all dependencies listed in package.json, but it also downloaded their dependencies and the dependencies of their dependencies etc., which is code you did not explicitly ask for. While you can point your direct dependencies to trustworthy sources, you have no control about anything further down the line. In short: this is a (known) security hazard. A recent example is here. Auditing code in npm helps, but the whole concept is a fundamental problem.

Dart and Deno are reducing the problem significantly since you have to name all dependencies, but it does not necessarily help you if that dependency itself is compromised.

The runtime of Deno as well as wasmtime use a sandbox-approach to mitigate that: you have to enable access explicitly to anything: A Deno program has very few permissions otherwise. From a security point of view, this is much better.

Node.js nor Python have no sandbox model and when loading libraries from the Internet, which both do a lot, do you always know what you get? 
# DENO

cons

* Deno deps are loaded from individually-controlled URLs, with no autorihy like NPM to run security scans or remove malware  
* these URLs can randomly deliver malware for a percentage of requests, so they can survive security scans  
* the content at these URLs can be legitimate today and malware tomorrow (then back)