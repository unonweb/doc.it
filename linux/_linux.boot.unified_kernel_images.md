
- https://github.com/uapi-group/specifications/blob/main/specs/unified_kernel_image.md
- combination of an UEFI boot stub program, a Linux kernel image, an optional initrd, and further resources in a single UEFI PE file
- file format for UKIs is PE/COFF (Portable Executable / Common Object File Format)
- UKIs are regular UEFI PE files, they can thus be signed as one for Secure Boot, protecting all of the individual resources listed above at once, and their combination. Standard Linux tools such as `sbsigntool` and `pesign` can be used to sign UKI files. 
- UEFI-Bootloader, Kernel und Ramdisk in eine einzige Datei verschmelzen
- UKIs verhindern Manipulation von initialen Ramdisks und machen den Bootprozess damit vertrauenswürdiger.