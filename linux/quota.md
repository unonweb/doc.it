# quota

## install

```sh
sudo apt-get install quota
```

## check fs support

To check if quotas are enabled on an ext4 filesystem, you can run the following command:

```sh
sudo dumpe2fs -h /dev/sdb9 | grep -i quota
```

# CLI

## init

initialize and create the necessary database files for implementing disk quotas over /point

```sh
sudo quotacheck -cug /point
# -c generates the initial quota database files
# -u checks and initializes user quotas
# -g checks and initializes group quotas
```

## set

```bash
sudo edquota -f /point frida # set disk quotas for frida
# Filesystem                   blocks       soft       hard     inodes     soft     hard
# /dev/loop8                        0     500000     600000          0      90       100
```

Consequently, our default editor opens with the contents of the default quota settings

- *soft block 500,000*: the user uses up to roughly 500MB (500,000 KB) of disk space with a warning issued when reaching that limit
- *hard block 600,000*: absolute maximum amount of disk space (roughly 600MB) that the user is allowed to use on this filesystem
- *soft inode 90*: the user can create up to 90 files or directories without receiving warnings
- *hard inode 100*: exact maximum number of inodes that the user can create files or directories

Alternatively, we can use the `setquota`:

```bash
setquota -u frida 500000 600000 90 100 -a /dev/loop8
```

## enable

```bash
sudo quotaon /point # activate disk quotas on the specific filesystem
```

## report

`repquota`  displays information about user and group quotas on filesystems. Thus,  this enables system administrators to monitor and manage resource usage  effectively.

```bash
sudo repquota -ug /point # displays disk quota information for users and groups on the filesystem
```

# How To

## use quota on a subtree of the filesystem

Filesystem quota on ext4 is per-user/group, not per-directory. A simple trick, though, is to create a 2GB file, create a virtual filesystem on it, and mount it at the desired folder.

Preparations:

```sh
mkdir /point
dd if=/dev/zero of=~/mydatafile bs=1M count=1024
```

Create a virtual filesystem:

```sh
mkfs.ext4 ~/mydatafile
# format the mydatafile with an ext4 filesystem, effectively turning it into a virtual disk within a file
sudo mount -o loop,rw,usrquota,grpquota ~/mydatafile /point
# loop treats mydatafile as a loopback device, enables mount like a regular block device or disk partition
# usrquota, grpquota enable user and group quotas on the mounted filesystem
```

At this point, we have a virtual filesystem mounted in the specified directory with size limits.

To make these settings persistent after a reboot, we can create an entry in the `/etc/fstab`:

```sh
sudo nano /etc/fstab
# --> /home/amir/mydatafile /point ext4 loop,rw,usrquota,grpquota 0 0
```

* The first zero indicates that the filesystem shouldn’t be automatically checked during boot. 
* The second zero means that the filesystem  shouldn’t be included in automatic backup operations.