
# LINKS

- https://manpages.debian.org/bookworm/apt-listchanges/apt-listchanges.1.en.html

# APT-LISTCHANGES

**apt-listchanges** is a tool to show what has been changed in a new version of a Debian package, as compared to the version currently installed on the system.


In den meisten Debian Paketen sind so genannte „Changelog“-Dateien enthalten. In diesen dokumentiert der Betreuer eines Pakets die Änderungen zu vorhergehenden Versionen. Ein Blick in diese Dateien nach einem Update ist bei der Fehlersuche hilfreich, wenn Probleme aufgetreten sind. Beispielsweise können in der neuen Version eines Pakets Dateien an einem anderen Ort liegen oder ein Dienst „horcht“ plötzlich auf einem anderen Port.

Da diese Änderungen erst nach der Installation bemerkt werden, kann es zu unerwünschten Unterbrechungen im Betrieb kommen. Abhilfe schafft hier das Paket `apt-listchanges`. Dieses zeigt vor der Installation eines Pakets die Veränderungen an, die im Changelog dokumentiert sind. Der Administrator hat die Möglichkeit, den Installationsvorgang an dieser Stelle abzubrechen.