## realpath

```sh
realpath <linkname> # To know which real file the link actually points to

# save absolute path to variable
pathGlobalAssets=$(realpath "../_global_assets/")
echo $pathGlobalAssets

domain="manueldieterich"
dest=$(realpath ..)/$domain # built absolute path from relative one
echo dest # /home/frida/code/web/projects/sites/11ty/manueldieterich
```
