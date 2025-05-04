---
title: '2. Users, Groups & File Permissions'
---
<div style="text-align: center;">
    <img src="https://cdn.hashnode.com/res/hashnode/image/upload/v1684174278610/5bfe372b-73a3-4385-a373-4eee0867e7c8.png" style="transform: scale(0.8);">
</div>

# Resources
1. https://linuxjourney.com/lesson/users-and-groupshttps://github.com/sunraymoonbeam/knowledge-base.wiki.git
1. https://linuxjourney.com/lesson/file-permissions
1. https://www.linuxfoundation.org/blog/blog//classic-sysadmin-understanding-linux-file-permissions
1. https://medium.com/@jasurbek.go.dev/users-groups-and-permissions-in-linux-1fa6d56b744a
1. https://www.multacom.com/faq/password_protection/file_permissions.htm
1. https://dev.to/izackv/running-a-docker-container-with-a-custom-non-root-user-syncing-host-and-container-permissions-26mb


# Table of Contents
0. [Summary](#summary)
1. [File Permissions](#file-permissions)
    1. [File Permissions Format](#file-permissions-format)
    1. [Numeric Representation](#numeric-representation)
    1. [File Permissions Commands](#file-permissions-commands)


## Summary
---
| S/N | Command                   | Description                                         | Example Usage                    |
| --- | ------------------------- | --------------------------------------------------- | -------------------------------- |
| 1   | `chmod`                   | Changes file permissions                            | `chmod 755 script.sh`            |
| 2   | `chown`                   | Changes file ownership                              | `sudo chown user:group file.txt` |


## File Permissions
In Unix-like operating systems, file permissions determine who can **read**, **write**, or **execute** a file. 

Permissions are assigned to three categories of users:
* **Owner**: User permissions apply only to the owner of the file or directory. They determine what actions the owner can perform on the file or directory.

* **Group**: Group permissions apply only to the group that has been assigned to the file or directory. They determine what actions the members of the group can perform on the file or directory.

* **Others**: Others permissions apply to all other users on the system. They determine what actions anyone else can perform on the file or directory.

### File Permissions Format
File permissions are displayed as a string of 10 characters, for example: `-rwxr-xr--`.

- The first character indicates the file type (`-` for a regular file, `d` for a directory, `l` for a symbolic link, etc.).
- The next nine characters are divided into three sets of three:
  - The first set represents the **owner’s** permissions.
  - The second set represents the **group’s** permissions.
  - The third set represents the **others’** permissions.

Each set can contain:
- `r` (read)
- `w` (write)
- `x` (execute)
- `-` (no permission)

#### Numeric Representation
<div style="text-align: center;">
    <img src="https://www.linode.com/docs/guides/linux-users-and-groups/1487-numeric-permissions.png" style="transform: scale(0.6);">
</div>

Recall that for each file, there are three sets of permissions: one for the **user** (owner), one for the **group**, and one for **others**. 

These permissions determine what actions can be performed on a file. The three basic permissions are:

- **Read (R)**: Allows reading or viewing the contents of the file (represented by the number **4**).
- **Write (W)**: Allows modifying or editing the file (represented by the number **2**).
- **Execute (X)**: Allows running the file if it is a script or program (represented by the number **1**).

Each of these permissions can be combined to form a specific set of access controls. The permission values for the user, group, and others are summed to form a three-digit number, where each digit represents the permissions for that group.

Common Numeric Representations:
* `777` - All can read, write, and execute (full access).
* `755` - Owner can read, write, and execute; group and others can read and execute.
* `644` - Owner can read and write; group and others can read only.

| Permission Type         | Numeric Value | 
|-------------------------|---------------|
| None                    | 0             | 
| Read                    | 4             | 
| Write                   | 2             |
| Execute                 | 1             | 
| Read + Write            | 6 (4+2)       | 
| Read + Execute          | 5 (4+1)       | 
| Write + Execute         | 3 (2+1)       | 
| Read + Write + Execute  | 7 (4+2+1)     |


## File Permissions Commands
```zsh   
    chmod
    chown
```
---
1. **chmod** - Change file permissions.
    ```zsh
    chmod permissions file
    ```
    - Common flags: `-R` (recursive)
    - Usage 1: `chmod u+x file` - Add execute permission for the owner.
    - Usage 2: `chmod g-w file` - Remove write permission for the group.
    - Usage 3: `chmod o+r file` - Add read permission for others.

2. **chown** - Change file owner and group.
    ```zsh
    chown owner:group file
    ```
    - Common flags: `-R` (recursive)
    - Usage: `chown user:group file.txt` - Change the owner and group of `file.txt`.





