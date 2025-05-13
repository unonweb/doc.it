# LINKS

- https://blog.verbum.org/2020/08/22/immutable-%e2%86%92-reprovisionable-anti-hysteresis/

# HYSTERESE

**Ein Systemverhalten, bei dem die Ausgangsgröße nicht allein von der unabhängig veränderlichen Eingangsgröße, sondern auch vom vorherigen Zustand der Ausgangsgröße abhängt.** Das System kann also – abhängig von der Vorgeschichte – bei gleicher Eingangsgröße einen von mehreren möglichen Zuständen einnehmen. Dieses Verhalten wird auch **Pfadabhängigkeit** genannt. 

Heizung
- Eingangsgröße: Soll-Temperatur
- Ausgangsgröße: Ist-Temperatur

Typisch für Hystereseverhalten ist das Auftreten einer **Hystereseschleife**, die entsteht, indem man die verursachende Größe zwischen zwei verschiedenen Werten hin und her bewegt. 

Das bekannteste Phänomen ist das Hystereseverhalten eines Ferromagneten in einem Magnetfeld: Wird ein nicht magnetisierter Ferromagnet einem externen Feld ausgesetzt und dieses danach ausgeschaltet, so behält der Ferromagnet je nach Polung (d. h. Richtung) des externen Feldes eine positive oder negative Magnetisierung.

**Hysteresis characterizes a system whose behavior (output) does not only depend on its input at time _t_, but also on its past behavior, on the _path_ it has followed.**

# IT

hysteresis
## Configuration management systems and hysteresis

-  https://blog.verbum.org/2020/08/22/immutable-%e2%86%92-reprovisionable-anti-hysteresis/

This "hysteresis" problem occurs not just in package managers but also many configuration management systems (puppet/ansible/etc).

A simple example I’ve seen happen is where the system administrator writes a playbook (or equivalent) that does e.g.:

```yml
- name: Allow nopasswd for wheel
  lineinfile:
    path: /etc/sudoers
    state: present
    regexp: '^%wheel ALL='
    line: '%wheel ALL=(ALL) NOPASSWD: ALL'
```

Then later, say the organization wants to change to use a separate group instead of `wheel`, say `admins` or whatever.

If the playbook is changed in git to do:

```yml
- group:
    name: admin
    state: present
- name: Allow nopasswd for admins
  lineinfile:
    path: /etc/sudoers
    state: present
    regexp: '^%admin ALL='
    line: '%admin ALL=(ALL) NOPASSWD: ALL'
```

The previous change to modify `wheel` in `/etc/sudoers` will _silently persist_ (until the system is reprovisioned). And that could become a security problem even in this case.

In most of these configuration management systems, in some cases the admin may need to explicitly add a change which **reverts a prior change**, and then makes the new change. But not all of the time – some (most) changes _don’t_ need this.

It’s an easy mistake to make when writing effectively arbitrary code to change files in persistent filesystems.

Hence, configuration management systems are subject to hysteresis too, and I think many of them could do better in warning users about this, and pushing for better practices. 

For example, the playbook would be more "**anti-hysteresis**" if it wrote to `/etc/sudoers.d/mycustom.conf` which gets replaced entirely, though `/etc/sudoers.d` is only supported by relatively modern sudo I think.

