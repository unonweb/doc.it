# dumpe2fs

**dump ext2/ext3/ext4 file system information**; prints the super block and blocks group information

Note: When used with a mounted file system, the printed information may be old or inconsistent.q

```sh
sudo dumpe2fs -h /dev/sda1
dumpe2fs -h # only display the superblock information and not any of the block group descriptor detail information
```

