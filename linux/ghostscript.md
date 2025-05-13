# ghostscript

* **compress pdf files**

```sh
sudo apt install  ghostscript
gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/screen -dNOPAUSE -dQUIET -dBATCH -sOutputFile=output.pdf input.pdf
```

The “dPDFSETTINGS" is where you’ll choose the file size.  Change the suffix “=/screen” to suit your need:

```sh
-dPDFSETTINGS=/screen 	# Low quality and small size at 72dpi.
-dPDFSETTINGS=/ebook 		# Slightly better quality but also a larger file size at 150dpi.
-dPDFSETTINGS=/prepress # High quality and large size at 300 dpi.
-dPDFSETTINGS=/default 	# System chooses the best output, which can create larger PDF files.
```
