```sh
sudo apt install libimage-exiftool-perl
```

```sh
exiftool -xmp:author:all -a image.jpg # Extract all author-related XMP information from an image.


exiftool -time:all -G -a -s
# -G[NUM...]  (-groupNames)        Print group name for each tag
# -a          (-duplicates)        Allow duplicate tags to be extracted
# -s[NUM]     (-short)             Short output format
```

