# tmpfs vs. swap

- tmpfs uses memory while as swap uses persistent storage devices.

- tmpfs can be viewed as file system in df output whereas swap dont

- swap has general size recommendations, tmpsfs not. tmpfs size varies on system purpose.

- tmpfs makes applications fasters on loaded systems. swap helps system breath in memory full situations.

- swap full indicates system heavily loaded, degraded performance and may crash. tmpfs being full not necessarily means heavy load or prone to crash.

- tmpfs is enhancement where as swap is must have feature!

## tmpfs

*tmpfs* ist ein Dateisystem, das in vielen Unix-artigen Betriebssystemen zum Anlegen einer RAM-Disk eingesetzt wird. Mit *tmpfs* können Teile des realen Arbeitsspeichers wie eine Festplatte eingebunden und beschrieben werden.

*tmpfs* is portion of a virtual memory mounted as a file system which helps to speed up applications. It normally being used to transfer data between programs. It appears as a file system but it does not use persistent device such as hard disk. Instead it uses virtual memory (portion of a RAM).

## swap

swap is portion of your hard disks used to extend RAM. Its roughly extended RAM by use of persistent storage device. swap only comes in action once your RAM (physical memory) is full. Normal thumb rule is size of swap should be double of your physical ram size.