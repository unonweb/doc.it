
- https://daniel.haxx.se/docs/curl-vs-wget.html
- recursive: 
  wget's major strong side compared to curl is its ability to download recursively, or even just download everything that is referred to from a remote resource, be it a HTML page or a FTP directory listing.
- wget requires no extra options to simply download a remote URL to a local file, while curl requires -o or -O
# CLI

```sh
wget -r http://example.com/ --no-parent 
# don't follow links to parent directories

-D domain-list
       --domains=domain-list
           Set domains to be followed.
```

```sh
wget -r -np -nc -A png,jpg,jpeg --wait=5 -e robots=off https://www.friederanders.de/zirkus-primel

wget --wait=5 # wait 5 seconds between retrievals. So that we don't thrash the server.
wget -r # recursive
wget --no-parent # don't follow links to parent directories; wget -np 

wget -nd # no directories): download all files to the current directory
wget -e robots=off # ignore restrictions in robots.txt file and don't download robots.txt files
wget -A png,jpg # accept only files with the extensions png or jpg
wget -m # (mirror): -r --timestamping --level inf --no-remove-listing


wget --level=<depth> # Set the maximum number of subdirectories that Wget will recurse into to depth.  In order to prevent one from accidentally downloading very large websites when using recursion this is limited to a depth of 5 by default; wget -l

wget --page-requisites # This option causes Wget to download all the files that are necessary to properly display a given HTML page. This includes such things as inlined images, sounds, and referenced stylesheets. Ordinarily, when downloading a single HTML page, any requisite documents that may be needed to display it properly are not downloaded.  Using -r together with -l can help, but since Wget does not ordinarily distinguish between external and inlined documents, one is generally left with "leaf documents" that are missing their requisites; wget -p
```

```sh
wget -r -np -nc -A png,jpg,jpeg --wait=5 -e robots=off https://www.friederanders.de/zirkus-primel

wget --wait=5 # wait 5 seconds between retrievals. So that we don't thrash the server.
wget -r # recursive
wget -np 
wget --no-parent # don't follow links to parent directories
wget -nd # no directories): download all files to the current directory
wget -e robots=off # ignore restrictions in robots.txt file and don't download robots.txt files
wget -A png,jpg # accept only files with the extensions png or jpg
wget -m # (mirror): -r --timestamping --level inf --no-remove-listing


wget -l <depth>
wget --level=<depth> # Set the maximum number of subdirectories that Wget will recurse into to depth.  In order to prevent one from accidentally downloading very large websites when using recursion this is limited to a depth of 5 by default

wget -p
wget --page-requisites # This option causes Wget to download all the files that are necessary to properly display a given HTML page. This includes such things as inlined images, sounds, and referenced stylesheets. Ordinarily, when downloading a single HTML page, any requisite documents that may be needed to display it properly are not downloaded.  Using -r together with -l can help, but since Wget does not ordinarily distinguish between external and inlined documents, one is generally left with "leaf documents" that are missing their requisites
```

# download options

```sh
wget -nc
wget --no-clobber # Skip download if files exist

wget --output-document=<file> # The documents will not be written to the appropriate files, but all will be concatenated together and written to file; wget -O
wget -O - # If - is used as file, documents will be printed to standard output
```

