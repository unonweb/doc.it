# LINKS

- https://github.com/linuxmint/timeshift

# TIMESHIFT

Timeshift is designed to **protect system files and settings**. It is NOT a backup tool and is **not meant to protect user data**. Entire contents of users' home directories are excluded by default.

Snapshots are saved by default on the system (root) partition in path **`/timeshift`**. Other linux partitions can also be selected. For best results the snapshots should be saved to an external (non-system) partition.

- remote and network locations are not supported
## btrfs mode

It is strongly recommended to use **BTRFS snapshots** on systems that are installed on BTRFS partition. BTRFS snapshots are perfect byte-for-byte copies of the system. Nothing is excluded. BTRFS snapshots can be created and restored in seconds, and have very low overhead in terms of disk space.
## rsync mode

In RSYNC mode, snapshots are taken using rsync and hard-links. Common files are shared between snapshots which saves disk space. Each snapshot is a full system backup that can be browsed with a file manager.