---
title: 0. Linux Cheatsheet
---
<div style="text-align: center;">
    <img src="https://media.geeksforgeeks.org/wp-content/uploads/20230714151733/linux-Cheat-Sheet.png" style="transform: scale(0.7);">
</div>


# Table of Contents
1. [Commands](#Summary)
    1. [Essentials](#basic-commands)
    1. [User, Groups and File Permissions](#user-groups-and-file-permissions)
    1. [Process Management](#process-management)
    1. [System Information](#system-information)
    1. [Package Management](#package-management)

1. [Cheatsheets](#cheatsheets)


## Top K Linux Commands
### Basic Commands
| S/N | Command         | Description                                         | Example Usage                    |
| --- | ----------------| --------------------------------------------------- | -------------------------------- |
|     |                 | **File Management**                                 |                                  |
| 1   | `touch`         | Creates an empty file                               | `touch file1.txt`                |
| 2   | `cp`            | Copies file or directory                            | `cp file1.txt backup/`           |
| 3   | `mv`            | Moves or renames a file or directory                | `mv oldname.txt newname.txt`     |
| 4   | `rm`            | Removes a file                                      | `rm unwanted.txt`                |
|     |                 | **Directory Management**                            |                                  |
| 5   | `pwd`           | Prints the current working directory                | `pwd`                            |
| 6   | `cd`            | Changes the current directory                       | `cd /var/www/html`               |
| 7   | `ls`            | Lists files and directories                         | `ls -lah`                        |
| 8   | `mkdir`         | Creates a new directory                             | `mkdir new_project`              |
| 9   | `rmdir`         | Removes an empty directory                          | `rmdir old_empty_folder`         |
|     |                 | **File Viewing**                                    |                                  |
| 10  | `cat`           | Displays file contents                              | `cat file1.txt`                  |
| 11  | `file`          | Displays file type                                  | `file image.png`                 |
| 12  | `less`          | Views file contents one page at a time              | `less file1.txt`                 |
| 13  | `head`          | Outputs the first part of files                     | `head -n 10 file1.txt`           |
| 14  | `tail`          | Outputs the last part of files                      | `tail -n 10 file1.txt`           |
|     |                 | **Text / Output Processing**                        |                                  |
| 15  | `sort`          | Sorts lines of text files                           | `sort file1.txt`                 |
| 16  | `uniq`          | Reports or omits repeated lines                     | `uniq file1.txt`                 |
| 17  | `find`          | Finds files matching a pattern in a specified path  | `find . -name "*.log" -type f`   |
| 18  | `grep`          | Searches for a pattern in a file                    | `grep "error" logs.txt`          |
| 19  | `wc`            | Counts lines, words, and characters in a file       | `wc -l file1.txt`                |
| 20  | `nl`            | Numbers the lines in a file                         | `nl file1.txt`                   |
| 21  | `\|`            | Pipes the output of one command to another          | `cat file1.txt \| grep "keyword"`|
|     |                 | **Environment Management**                          |                                  |
| 22  | `echo`          | Prints the value of a variable                      | `echo $HOME`                     |
| 23  | `export`        | Sets an environment variable                        | `export MLFLOW_TRACKING_USERNAME=xxx`           |
| 24  | `env`           | Sets and runs a command with a temporary variable   | `env MLFLOW_TRACKING_USERNAME=xxx printenv`     |
| 25  | `printenv`      | Displays all environment variables                  | `printenv`                       |
| 26  | `unset`         | Removes an environment variable                     | `unset MLFLOW_TRACKING_PASSWORD` |
|     |                 | **Others**                                          |                                  |
| 27  | `which`         | Shows the full path of shell commands               | `which python`                   |
| 28  | `alias`         | Creates an alias for a command                      | `alias ll='ls -la'`              |
| 29  | `tee`           | Reads from standard input and writes to standard output and files | `echo "Hello" \| tee output.txt`   |
| 30  | `history`       | Shows the command history                           | `history`                        |
| 31  | `clear`         | Clears the terminal screen                          | `clear`                          |
| 32  | `man`           | Displays the manual for a command                   | `man ls`                         |
| 33  | `time`          | Measure the duration of command execution.          | `time ls`                        |

## File Permissions
| 1   | `ls -l`   | Lists files with permission info in long format | `ls -l`                           |
| 2   | `chmod`   | Changes file/directory permissions              | `chmod 755 script.sh`             |
| 3   | `chown`   | Changes file owner and group                    | `sudo chown user:group file.txt`  |
| 4   | `chgrp`   | Changes group ownership of a file               | `sudo chgrp devs report.txt`      |
| 5   | `umask`   | Sets default permission mask for new files      | `umask 022`                       |
| 6   | `getfacl` | Views ACL (Access Control List) permissions     | `getfacl file.txt`                |
| 7   | `setfacl` | Sets fine-grained ACL permissions               | `setfacl -m u:alice:r-- file.txt` |

### User & Groups Permissions
| S/N | Command         | Description                                         | Example Usage                    |
| --- | ----------------| --------------------------------------------------- | -------------------------------- |
| 1   | `su`            | Switches to another user                            | `su root`                        |
| 2   | `sudo`          | Runs a command as another user, usually root        | `sudo apt update`                |
| 3   | `whoami`        | Displays the current user                           | `whoami`                         |
| 4   | `useradd`       | Adds a new user                                     | `sudo useradd alice`             |
| 5   | `groupadd`      | Adds a new group                                    | `sudo groupadd developers`       |

### Process Management
| S/N | Command         | Description                                         | Example Usage                    |
| --- | ----------------| --------------------------------------------------- | -------------------------------- |
| 1   | `ps`            | Displays running processes                          | `ps aux`                         |
| 2   | `top`           | Displays real-time system processes                 | `top`                            |
| 3   | `kill`          | Terminates a process                                | `kill 1234`                      |

### System Information
| S/N | Command         | Description                                         | Example Usage                    |
| --- | ----------------| --------------------------------------------------- | -------------------------------- |
| 1   | `uname`         | Prints system information                           | `uname -mp`                      |
| 2   | `df`            | Displays disk space usage                           | `df -h`                          |
| 3   | `du`            | Displays disk space usage                           | `du -h`                          |
| 4   | `lscpu`         | Displays information about the CPU architecture     | `lscpu`                          |
| 5   | `lshw`          | Lists detailed information about hardware configs   | `lshw -short`               |
| 6   | `lsblk`         | Lists information about block devices               | `lsblk -f`                       |


### Package Management
| S/N | Command                   | Description                                         | Example Usage                    |
| --- | ------------------------- | --------------------------------------------------- | -------------------------------- |
| 1   | `apt-get update`          | Updates package information                         | `apt-get update`                 |
| 2   | `apt-get upgrade`         | Upgrades all packages to their latest versions      | `apt-get upgrade`                |
| 3   | `apt-get install`         | Installs the specified package and dependencies     | `apt-get install curl=7.68.0-1ubuntu2.6`      |
| 4   | `apt-get reinstall`       | Reinstalls a package and its dependencies           | `apt-get reinstall curl`         |
| 5   | `apt-get remove`          | Removes the specified package                       | `apt-get remove curl`            |
| 6   | `apt-get purge`           | Removes the package and its configuration files     | `apt-get purge curl`             |
| 7   | `apt-get autoremove`      | Removes any unused dependencies                     | `apt-get autoremove`             |
| 8   | `apt-get list --installed`| Lists all installed packages                        | `apt-get list --installed`       |
| 9   | `apt-get show`            | Displays information about a package                | `apt-get show curl`              |
| 10  | `apt-get search`          | Searches for packages                               | `apt-get search curl`            |
| 11  | `dpkg -i`                 | Installs a package                                  | `dpkg -i some_deb_package.deb`   |
| 12  | `dpkg -r`                 | Removes a package                                   | `dpkg -r some_deb_package.deb`   |
| 13  | `dpkg -l`                 | Lists installed packages                            | `dpkg -l`                        |
| 11  | `dpkg -i`                 | Installs a package                                  | `dpkg -i some_deb_package.deb`   |
| 12  | `dpkg -r`                 | Removes a package                                   | `dpkg -r some_deb_package.deb`   |
| 13  | `dpkg -l`                 | Lists installed packages                            | `dpkg -l`                        |

Compiled by Ren Hwa


## Cheatsheets
![image](https://media.licdn.com/dms/image/v2/D5622AQHoqxyfrEBXhw/feedshare-shrink_800/feedshare-shrink_800/0/1695863730795?e=2147483647&v=beta&t=EJ0Ih_mD4JTC77ATDvYnJHNE2UOFphET0NmdlAPcQiw)
