# ln

* hard links aren’t allowed for directories in Linux

```sh
ln [OPTION]... [-T] TARGET LINK_NAME
```

```sh
ln my_file.txt my_link.txt # creates a hard link to a file
ln -s /mnt/my_drive/movies ~/my_movies # creates a sym link to a directory
ln -s my_file.txt my_link.txt # creates a sym link to a file

ln -r
ln --relative # create symbolic links relative to link location
ln -s
ln --symbolic # make symbolic links instead of hard links
ln -t
ln --target-directory=<dir> # specify the directory in which to create the links

# absolute paths
ln -s `pwd`/relative/path.txt ./new/path.txt # creates a symlink with an absolute path

ln -f
ln --force # remove existing destination files
```

```sh
# find all hardlinks belonging to a file
find ~/code/web/projects/sites/11ty/ -samefile ./layout_page_global.11ty.cjs
```
