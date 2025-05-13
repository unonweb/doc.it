# diff

compare files line by line

```sh
diff --brief # report only when files differ; diff -q
diff --side-by-side # output in two columns; diff -y
diff --report-identical-files # report when two files are the same; diff -s
diff --exclude=<PAT> # exclude files that match PAT; diff -x
diff --ignore-case # ignore case differences in file contents; diff -i
diff --ignore-all-space # ignore all white space; diff -w

diff --recursive # recursively compare any subdirectories found
diff --no-dereference # don't follow symbolic links
```

```bash
sudo diff --brief --recursive --exclude /mnt /mnt/debian /mnt/debian-snap-4
```