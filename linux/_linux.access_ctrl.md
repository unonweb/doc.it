# DAC

**Discretionary Access Control**

- discretion
  Ermessen
- use your own discretion
  Sie müssen nach eigenem Ermessen handeln
- to do sth at one’s discretion
  etw nach eigenem Ermessen tun

DAC restricts access to resources based on users and/or groups they belong. E.g. owner of a file can set read, write and execute permissions and so a user is in control of who can access and modify a resource. In other words, access to a resource is at the user’s discretion. 

The controls are discretionary in the sense that a subject with a certain access permission is capable of passing that permission (perhaps indirectly) on to any other subject (unless restrained by mandatory access control)"

#### [security]

This creates a problem where a compromised program inherits access controls of the user and so can-do things that users can do. If a compromised process happens to be running with effective superuser privileges, an attacker can take full control of the system.

# MAC

Instead of deciding what a program can and cannot do based on DAC measures, it is more secure to let programs only do what they need to perform their tasks. So even if a program runs with effective root privileges, it cannot do anything other than it is allowed to do. This type of control over capabilities and permissions is called Mandatory Access Control (MAC). 

# OTHER
## Zugriffsrechte

```sh
find / -perm -4000 
find / -perm -u+s # zeigt alle Programme bei denen das SUID-Bit gesetzt ist
```

## log

```sh
tail -e /var/log/auth.log # sh alle Versuche, sich am System anzumelden, werden hier protokolliert. Dabei ist egal, ob sich jemand an einer Konsole oder über ssh anmeldet oder versucht wird, per sudo Root-Rechte zu erlangen
```

