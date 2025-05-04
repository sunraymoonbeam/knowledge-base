---
title: 5.  Linux File System
---
# Linux Filesystem Hierarchy Standard
The Linux Filesystem Hierarchy Standard (FHS) is a set of guidelines that organizes how files and directories are structured on a Linux system. It helps ensure that important system files, user data, and program files are kept in predictable places, making it easier for users and administrators to manage the system. Understanding the FHS can help you navigate and maintain a Linux system more easily and troubleshoot issues.


<div style="text-align: center;">
    <img src="https://miro.medium.com/v2/resize:fit:1400/1*L1wPdPxYGAQETg5wnJS6nw.png" style="transform: scale(0.6);">
</div>


| Directory | Description |
|-----------|-------------|
| `/`       | The root filesystem is the top-level directory of the filesystem. It must contain all of the files required to boot the Linux system before other filesystems are mounted. It must include all of the required executables and libraries required to boot the remaining filesystems. After the system is booted, all other filesystems are mounted on standard, well-defined mount points as subdirectories of the root filesystem. |
| `/bin` | The `/bin` directory contains user executable files. |
| `/boot` | Contains the static bootloader and kernel executable and configuration files required to boot a Linux computer. |
| `/dev` | This directory contains the device files for every hardware device attached to the system. These are not device drivers, rather they are files that represent each device on the computer and facilitate access to those devices. |
| `/etc` | Contains the local system configuration files for the host computer. |
| `/home` | Home directory storage for user files. Each user has a subdirectory in `/home`. |
| `/lib` | Contains shared library files that are required to boot the system. |
| `/media` | A place to mount external removable media devices such as USB thumb drives that may be connected to the host. |
| `/mnt` | A temporary mount point for regular filesystems (not removable media) that can be used while the administrator is repairing or working on a filesystem. |
| `/opt` | Optional files such as vendor-supplied application programs should be located here. |
| `/root` | This is not the root (`/`) filesystem. It is the home directory for the root user. |
| `/sbin` | System binary files. These are executables used for system administration. |
| `/tmp` | Temporary directory. Used by the operating system and many programs to store temporary files. Users may also store files here temporarily. Note that files stored here may be deleted at any time without prior notice. |
| `/usr` | These are shareable, read-only files, including executable binaries and libraries, man files, and other types of documentation. |
| `/var` | Variable data files are stored here. This can include things like log files, MySQL, and other database files, web server data files, email inboxes, and much more. |


