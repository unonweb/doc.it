# dash

Dash steht für **D**ebian **A**lmquist **Sh**ell. Die Dash ist kleiner und schneller als z.B. die Bash und erfüllt vollständig die POSIX-Spezifikation. Seit Ubuntu 6.10 ist die Dash die Standard nicht-interaktive Shell von Ubuntu. Dies wird erreicht, indem **/bin/sh** ein Symlink auf **/bin/dash** ist.

Die Dash ist allerdings im interaktiven Modus eher unkomfortabel. Deshalb  ist der Standard für die interaktive Shell (also die Shell, auf der man arbeitet, wenn man ein Terminal öffnet) die Bash.

Leider gibt es immer wieder Shellskripte, die nicht vollständig POSIX-kompatibel sind und nur funktionieren, wenn statt der Dash die  Bash benutzt wird.

```shell
#!/bin/sh
# --> /usr/bin/dash
#!/bin/bash
# --> /usr/bin/bash
```

Dash shines in:

- Speed of execution. Roughly **4x times faster** than Bash and others.
- Very limited resources (disk space, RAM or CPU). As minimalistic as  possible - much smaller (134.1 kB vs 6.5 MB installed) than Bash and others.