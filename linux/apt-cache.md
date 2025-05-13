```bash
apt-cache depends <pkg> # Which dependencies does a given package have?
apt-cache rdepends <pkg> # Which packages depend on the given package?

apt-cache rdepends --implicit # Per default depends and rdepends print only dependencies explicitly expressed in the metadata. With this flag it will also show dependencies implicitly added based on the encountered data.
apt-cache rdepends --recurse # Make depends and rdepends recursive so that all packages mentioned are printed once.
apt-cache rdepends --installed # Limit the output of depends and rdepends to packages which are currently installed.

apt-cache policy <pkg> # ausführliche Übersicht der aktivierten Quellen
```

