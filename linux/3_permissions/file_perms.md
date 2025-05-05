---
title: Permissions
---

<div style="text-align: center;">
    <img src="https://cdn.hashnode.com/res/hashnode/image/upload/v1684174278610/5bfe372b-73a3-4385-a373-4eee0867e7c8.png" style="transform: scale(0.8);">
</div>

# Resources
1. [https://linuxjourney.com/lesson/file-permissions](https://linuxjourney.com/lesson/file-permissions)
2. [https://www.linuxfoundation.org/blog/blog//classic-sysadmin-understanding-linux-file-permissions](https://www.linuxfoundation.org/blog/blog//classic-sysadmin-understanding-linux-file-permissions)
3. [https://medium.com/@jasurbek.go.dev/users-groups-and-permissions-in-linux-1fa6d56b744a](https://medium.com/@jasurbek.go.dev/users-groups-and-permissions-in-linux-1fa6d56b744a)
4. [https://www.multacom.com/faq/password\_protection/file\_permissions.htm](https://www.multacom.com/faq/password_protection/file_permissions.htm)
5. [https://www.liquidweb.com/blog/what-is-umask-and-how-to-use-it-effectively/#:~:text=Umask%20(short%20for%20user%20file,masking%20or%20subtracting%20these%20permissions.](https://www.liquidweb.com/blog/what-is-umask-and-how-to-use-it-effectively/#:~:text=Umask%20(short%20for%20user%20file,masking%20or%20subtracting%20these%20permissions)


# Table of Contents
0. [Summary](#summary)
1. [Introduction to Permissions](#introduction-to-permissions)
1. [Permission Representation](#permission-representation)
    - [Symbolic](#symbolic)
    - [Octal](#octal)
1. [Key Commands](#key-commands)
    - [ls -l](#ls-l)
    - [chmod](#chmod)
    - [chown](#chown)
    - [chgrp](#chgrp)
    - [umask](#umask)


## Summary
---
| S/N | Command   | Description                                     | Example Usage                     |
| --- | --------- | ----------------------------------------------- | --------------------------------- |
| 1   | `ls -l`   | Lists files with permission info in long format | `ls -l`                           |
| 2   | `chmod`   | Changes file/directory permissions              | `chmod 755 script.sh`             |
| 3   | `chown`   | Changes file owner and group                    | `sudo chown user:group file.txt`  |
| 4   | `chgrp`   | Changes group ownership of a file               | `sudo chgrp devs report.txt`      |
| 5   | `umask`   | Sets default permission mask for new files      | `umask 022`                       |
| 6   | `getfacl` | Views ACL (Access Control List) permissions     | `getfacl file.txt`                |
| 7   | `setfacl` | Sets fine-grained ACL permissions               | `setfacl -m u:alice:r-- file.txt` |


## Introduction to Permissions
---
File and directory permissions in Unix-like operating systems control how users interact with files and directories. These permissions define who can perform specific actions (**READ (`r`)**, **WRITE (`w`)**, or **EXECUTE (`x`)**) and what those actions are.

Permissions are assigned to three categories of users:
* **Owner**: User permissions apply only to the owner of the file or directory. They determine what actions the owner can perform on the file or directory.

* **Group**: Group permissions apply only to the group that has been assigned to the file or directory. They determine what actions the members of the group can perform on the file or directory.

* **Others**: Others permissions apply to all other users on the system. They determine what actions anyone else can perform on the file or directory.

### File Permissions
File permissions determine what actions can be performed on a file:

* Read (r): Allows the user to view the contents of the file.
* Write (w): Allows the user to modify or delete the file.
* Execute (x): Allows the user to run the file as a program or script (e.g., a shell script or binary).

A private key file (e.g., id_rsa.pem) used for SSH connections should only be readable by the owner to ensure security:
```bash 
   chmod 400 id_rsa.pem
```
This command sets the permissions to `r--------`, meaning only the owner can read the file, and no one else can read, write, or execute it. SSH will refuse to use it if it's world-readable.

### Directory Permissions
Directory permissions determine how users interact with the contents of a directory:

* Read (r): Allows the user to list the files in the directory.
* Write (w): Allows the user to create, delete, or rename files within the directory.
* Execute (x): Allows the user to access the directory (e.g., cd into it) and read file metadata (e.g., file names).

A shared project directory where team members can collaborate:q 
```bash
   chmod 770 project_dir
```
This command sets the permissions to `rwxrwx---`, meaning the owner and group can read, write, and execute files in the directory, while others have no access. This allows team members to collaborate without exposing the directory to unauthorized users.

Note that directory permissions don’t Override File Permissions Ownership, ownership and  permissions are evaluated separately for each object. Just because Zack owns the directory doesn't mean he can access files he doesn't own, and just because Rachel owns the file doesn't mean she can delete it (unless she also has write permission on the directory).


## Permission Representation
---
### Symbolic Representation
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

We can use the `ls -l` command to view the permissions of files and directories in a directory. The output will show the permissions in a long format like this:

```zsh
total 8
drwxr-xr-x   5 lowrenhwa  staff   160B May  5 12:02 .
drwxr-xr-x  13 lowrenhwa  staff   416B May  5 12:02 ..
drwxr-xr-x   4 lowrenhwa  staff   128B May  5 12:02 target
-rw-r--r--@  1 lowrenhwa  staff    61B May  5 11:17 test.txt
drwxr-xr-x   4 lowrenhwa  staff   128B May  5 11:04 source
```
Explanation of the long format output:

1. **File permissions**: The first column indicates the file type and permission settings. For example, for the `source` directory, the permission is `drwxr-xr-x`. 
    - The first character (d or -) tells us if it's a directory (d) or a regular file (-). 
    - The next 9 characters are grouped into sets of 3 for owner, group, and others:
        - `rwx` — owner can read, write, and execute.
        - `r-x` — group can read and execute, but not write.
        - `r-x` — others can also read and execute.

2. **Number of links**: The second column shows the number of hard links to the file or directory. A hard link is essentially another name for the same file on disk. Both links point to the same underlying inode (the data structure representing the file). If one name is deleted, the data remains accessible as long as at least one link still exists. Regular files (like test.txt) usually have a value of 1, meaning there's only one directory entry linking to that file's inode. 

3. **Owner**: The third column shows the owner of the file or directory, which is `lowrenhwa` in this case.

4. **Group**: The fourth column shows the group associated with the file or directory, which is `staff` in this case.

5. **Size**: The fifth column shows the size of the file or directory in bytes.

6. **Modification Date**: The sixth, seventh, and eighth columns show the date and time of the last modification.
---

### Octal Representation
---
Recall that for each file, there are three sets of permissions: one for the **user** (owner), one for the **group**, and one for **others**. 

These permissions determine what actions can be performed on a file. The three basic permissions are:

- **Read (R)**: Allows reading or viewing the contents of the file (represented by the number **4**).
- **Write (W)**: Allows modifying or editing the file (represented by the number **2**).
- **Execute (X)**: Allows running the file if it is a script or program (represented by the number **1**).

Each of these permissions can be combined to form a specific set of access controls. The permission values for the user, group, and others are summed to form a three-digit number, where each digit represents the permissions for that group.

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

Common Numeric Representations:
| Octal | Permissions | Meaning                                |
| ----- | ----------- | -------------------------------------- |
| 777   | rwxrwxrwx   | Full access for all                    |
| 755   | rwxr-xr-x   | Owner can write; others read & execute |
| 644   | rw-r--r--   | Owner can write; others read-only      |

<div style="text-align: center;">
    <img src="https://www.linode.com/docs/guides/linux-users-and-groups/1487-numeric-permissions.png" style="transform: scale(0.6);">
</div>
---


## Key Commands
```zsh   
    ls -l
    chmod
    chown
    chgrp
    umask
```
---
1. **ls -l** - List files with detailed permissions. This command lists files in long format, displaying file type, permissions, number of links, owner, group, size, and last modification date.
    ```zsh
    ls -l [directory]
    ```
    - Usage: `ls -l` - List files in the current directory with permissions in long format.

2. **chmod** - Change file permissions. This command is used to modify the permission settings of a file or directory. You can use symbolic (u, g, o, a) or octal modes.
    ```zsh
    chmod permissions file
    ```
    - Common flags: `-R` (recursive)
    - Usage 1: `chmod u+x file` - Add execute permission for the owner.
    - Usage 2: `chmod g-w file` - Remove write permission for the group.
    - Usage 3: `chmod o+r file` - Add read permission for others.
    - Usage 4: `chmod 755 file` - Set permissions to `rwxr-xr-x`.
    - Usage 5: `chmod -R 755 folder` - Recursively set permissions for a directory.
    
3. **chown** - The `chown` command changes the ownership of a file or directory. It can update the user, group, or both.
    ```zsh
    chown owner:group file
    ```
    - Common flags: `-R` (recursive)
    - Usage: `chown user:group file.txt` - Change the owner and group of `file.txt`.
    - Usage 1: chown user file.txt – Change the owner of file.txt.
    - Usage 2: chown :group file.txt – Change only the group.
    - Usage 3: chown user:group file.txt – Change both owner and group.
	- Usage 4: sudo chown -R user:group folder/ – Recursively change owner and group of all files in a directory.

4. **chgrp** - Change group ownership of a file.
    ```zsh
    chgrp group file
    ```
    - Common flags: `-R` (recursive)
    - Usage 1: `chgrp developers report.txt` – Set the group of report.txt to developers.
	- Usage 2: `sudo chgrp -R devs /var/www` – Recursively change the group of all files in /var/www


5. **umask** - Set default file permissions for newly created files and directories.

    Umask (short for user file-creation mode mask) is used by UNIX-based systems to set default permissions for newly created files and directories. It does this by masking or subtracting these permissions. For example, with the usual Umask being set to 022 on most systems all the new files we create will subtract the Umask value from full permissions (for files that would be 666 – 022 = 644). Umask can be expressed in octal or symbolic values. 

    The `umask` subtracts from the full permission set:

    * Default file permission: `666`
    * Default directory permission: `777`

    For example:

    * `umask 022` → files get `644`, directories get `755`
    * `umask 077` → files get `600`, directories get `700`

    The Linux umask command is very useful when there are multiple users that are creating new files and directories, particularly in any kind of shared environment. System administrators can ensure that with properly set umask values, different users will make files with secure permissions by default instead of fixing and manually setting those later on, which can be risky, complicated, and time intensive.

    ```zsh
    umask [mask]
    ```
    - Usage: `umask 022` - Set the default mask to `022`, allowing read and execute permissions for group and others, but not write.
    - Usage 1: `umask` - View the current umask value.
    - Usage 2: `umask 077` - Set the umask to `077`, allowing only the owner to read and write files, while others have no permissions.
---

## Access Control Lists (ACLs)
---
TODO: Add a section on ACLs.

ACLs allow more granular permissions beyond user/group/others.

### `getfacl` – View ACLs

```bash
getfacl file.txt
```

### `setfacl` – Set ACLs

```bash
setfacl -m u:alice:r-- file.txt      # Add read permission for user alice
setfacl -x u:alice file.txt          # Remove ACL for user alice
```

Use ACLs when multiple users need custom access outside standard group settings.

---














