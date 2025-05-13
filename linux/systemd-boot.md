# LINKS

- https://systemd.io/BOOT/
- https://gnulinux.ch/in-den-tiefen-des-boot-vorgangs-systemd-boot

# SYSTEMD-BOOT

- UEFI boot-manager
- executes configured EFI images
- minimal alternative to GRUB (for systems using UEFI)
- operates on the EFI System Partition (ESP) only
  Configuration file fragments, kernels, initrds, other EFI images need to reside on the ESP.
- Linux kernels need to be built with **CONFIG_EFI_STUB** to be able to be directly executed as an EFI image.