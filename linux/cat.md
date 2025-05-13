# cat

concatenate multiple files

```sh
cat --show-all # equivalent to the following:
cat --show-ends # display $ at end of each line
cat --show-tabs # display TAB characters as ^I
cat --show-nonprinting # use ^ and M- notation, except for LFD and TAB
```

```sh
cat *.css > bundle.css # a)
for i in *.css; do cat $i >> bundle.css; done # b)
for f in *.css; do cat $f >> bundle.css; echo >> bundle.css; done; # c) inserts a new line between the files
```

```sh
cat file1.txt file2.txt file3.txt > file4.txt
```

# cat  & here documents

The most common syntax for here documents, is `<<` followed by a delimiting identifier (often the word `EOF` or `END`), followed, starting on the next line, by the text to be quoted, and then closed by the same delimiting identifier on its own line.

```sh
cat > /var/lib/AccountsService/users/ansible  <<EOF
[User]  
XSession=gnome  
SystemAccount=true  
EOF
```



