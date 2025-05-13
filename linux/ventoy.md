# VENTOY

The swiss army knife for creating bootable USBs

Ventoy is a “tiny OS” that boots your ISO.

When you create a bootable drive in the traditional way – by using a tool like Rufus, Etcher or dd on Linux, the drive only boots that particular distribution. When you use Ventoy, it makes a tiny bootable EFI partition on your USB drive and gives you the remaining space to simply copy ISO files to it.

This means, you create a Ventoy disk once and then all you have to do is update the ISO files like a normal USB stick. Pretty cool because it allows you to have multiple operating systems on one USB disk.

# how to

* https://www.ventoy.net/en/doc_start.html
* download https://github.com/ventoy/Ventoy/releases?ref=itsfoss.com
* extract
* run `VentoyGUI.x86_64`

Once Ventoy has been installed on the USB disk, you’ll have two partitions on it now.

The partition named VTOYEFI is reserved for the boot files by Ventoy.  The first partition, named Ventoy, is an exFAT partition. This is the  partition which will be used for copying the ISO file.

* download linux distribution
* copy the ISO files onto the drive named `Ventoy`