# CLI

```sh
pandoc -s -f markdown -t rtf -o file.rtf file.txt

pandoc --from <format> --to=FORMAT --output <file>

pandoc -f <format> -t <format> -o <file>

pandoc -s
pandoc --standalone # Produce output with an appropriate header and footer (e.g.  a standalone HTML, LaTeX, TEI, or RTF file, not a fragment).  This option is set automatically for pdf, epub, epub3, fb2, docx, and odt output. For native output, this option causes metadata to be included; otherwise, metadata is suppressed.
```

