# cp

```sh
cp [options] <source> <dest>
```

**archive**

```sh
cp --archive # Beibehaltung von Besitzer-, Gruppen- und Zugriffsrechten und Meta-Info; cp -a
# same as cp -dR --preserve=all
cp --preserve=links; cp -d
cp --recursive # copy directories recursively; cp -r, cp -R
cp --preserve[=ATTR_LIST]
```

```sh
cp --verbose # cp -v
cp --one-file-system # stay on this file system; cp -x
cp --target-directory <dir> # copy all SOURCE arguments into DIRECTORY
cp --no-target-directory # treat DEST as a normal file
```

```sh
cp --update # kopiert nur, wenn Zieldatei älter als Quelldatei
cp --no-clobber # do not overwrite an existing file
```

```sh
cp --no-dereference # symbolische Links kopieren, statt den Links in der Quelle zu folgen; cp -P
cp --symbolic-link # make symbolic links instead of copying; cp -s 
cp --link # kopiert nicht, sondern erstellt harten Link; cp -l
```
