## Free

```sh
$ df -h
```

## Mount

Contrôle
```sh
sudo mountpoint -- "$(docker info -f '{{ .DockerRootDir }}')"
/var/lib/docker is not a mountpoint
```

List partitions with filesystems types
```sh
$ lsblk -f

NAME    FSTYPE    LABEL        UUID                                    MOUNTPOINT
sda
├─sda1  ext4                   0935df16-40b0-4850-9d47-47cd2daf6e59
sdb
├─sdb1  ext4                   b9df59e6-c806-4851-befa-12402bca5828    /
```

Alternative (useful to get block ID)
```sh
$ blkid
/dev/sda1: TYPE="ext4" PARTUUID="0935df16-40b0-4850-9d47-47cd2daf6e59"
/dev/sdb1: UUID="b9df59e6-c806-4851-befa-12402bca5828" TYPE="ext4" PARTUUID="269f8db7-90b7-4ebe-acf4-521d6dc2eadb"
```

List of mounted points
```sh
$ findmnt /dev/sda1
TARGET SOURCE    FSTYPE OPTIONS
/boot  /dev/sda1 xfs    rw,relatime,seclabel,attr2,inode64,noquota

$ findmnt /boot
TARGET SOURCE    FSTYPE OPTIONS
/boot  /dev/sda1 xfs    rw,relatime,seclabel,attr2,inode64,noquota

# List of mounted points
$ mount
```


Mount a DOS floppy disk, a CDROM, etc...
```sh
# mount <device> <dir>, -t to explicitely name FS type (by default mount will guess)
$ mount -t msdos /dev/fd0 /mnt/floppy     # DOS floppy disk
$ mount -t iso9660 /dev/cdrom /mnt/cdrom  # CDROM
$ mount -t vfat /dev/hda1 /win            # FAT32
$ mount -t ntfs /dev/hda1 /win            # NTFS
```

Persist mount points in /etc/fstab
```sh
$ cat /etc/fstab

# /etc/fstab: static file system information. 
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system>              <mount point>         <type>  <options>          <dump>  <pass>
UUID=b9df59e6-c806        /                     ext4    errors=remount-ro  0       1
```

Unmount
```sh
# umount <dir>
$ umount /mnt/floppy
$ umount /mnt/cdrom
$ umount -l <device|directory>  # lazily
$ umount -f <device|directory>  # force (may lose data)
```


## Usage

Options \
-a : list files and directories (default : only show directories) \
-c : grand total \ 
-d : depth \
-h : human readable \
-s : summarize, only display total \
-x : skip directories on different file systems (--one-file-system)

Size of a file
```sh
$ du -h foo.txt
12K     foo.txt
```

Size of a folder (full details, depth 1, sorted)
```sh
# Solution 1
$ du -ahc -d 1 . | sort -hr
16K     ./folder
...
1.5M    ./foo.txt
18M     .

$ ls | xargs -I {} du -shx {}

# Solution 2 (se placer dans le dossier)
$ du -csh *
```

Size of a folder (no details)
```sh
$ du -hs .
```
