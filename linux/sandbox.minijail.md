# LINKS

- https://google.github.io/minijail/
- https://google.github.io/minijail/minijail0.1

# IMPLEMENTATION

- https://google.github.io/minijail/minijail0.1#implementation

This program is broken up into two parts: **minijail0** (the frontend) and a helper library called **libminijailpreload**. Some jailings can only be achieved from the process to which they will actually apply:

- **capability** use (without using ambient capabilities): non-ambient capabilities are not inherited across `execve` unless the file being executed has POSIX file capabilities. Ambient capabilities (the **--ambient** flag) fix capability inheritance across `execve` to avoid the need for file capabilities.

- **seccomp**: a meaningful seccomp filter policy should disallow `execve`, to prevent a compromised process from executing a different binary. However, **this would prevent the seccomp policy from being applied before `execve`**.

To this end, **libminijailpreload** is forcibly loaded into all dynamically-linked target programs by default; we pass the specific restrictions in an environment variable which the preloaded library looks for. The forcibly-loaded library then applies the restrictions to the newly-loaded program.