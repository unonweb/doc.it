## mv

**rename/move**

```sh
mv -i
mv --interactive # prompt before overwrite

mv -t 
mv --target-directory <directory> # move all source arguments into directory

mv -u 
mv --update # move only when the SOURCE file is newer than the destination file or when the destination file is missing

mv -v
mv --verbose # explain what is being done
```

```sh
mv unonweb/* . # move the contents of unonweb/ to the current directory (DOES NOT MOVE HIDDEN FILES!)
mv unonweb/.[!.]* . # moves hidden files, too
```
