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
1. [Introduction: The need for users, groups and permissions](#introduction)
1. [User Accounts](#user-accounts)
    1. [`/etc/passwd`](#etcpasswd)
    1. [User Management Commands](#user-management-commands)
1. [User Groups](#user-groups)
    1. [`/etc/group`](#etcgroup)
    1. [User Group Management Commands](#user-group-management-commands)
1. [Case Study: Running a Docker Container with a Custom Non-Root User](#case-study)


## Summary
---
| S/N | Command                   | Description                                         | Example Usage                    |
| --- | ------------------------- | --------------------------------------------------- | -------------------------------- |
| 1   | `su`                      | Switches to another user                            | `su root`                        |
| 2   | `sudo`                    | Runs a command as another user, usually root        | `sudo apt update`                |
| 3   | `whoami`                  | Displays the current user                           | `whoami`                         |
| 4   | `useradd`                 | Adds a new user                                     | `sudo useradd alice`             |
| 5   | `groupadd`                | Adds a new group                                    | `sudo groupadd developers`       |
| 6   | `chmod`                   | Changes file permissions                            | `chmod 755 script.sh`            |
| 7   | `chown`                   | Changes file ownership                              | `sudo chown user:group file.txt` |


## Introduction
### The need for users, groups and permissions
---
TBC


## User Accounts
Linux can support multiple users, and there are three main types of Linux user accounts. Every user account has a unique User ID (UID) and belongs to a group with a Group ID (GID).

- **Root user**: The root user, also known as the superuser, has unrestricted access to all files and commands on the system. The root user can perform administrative tasks such as changing file permissions, creating users, and installing software. UID and GID = 0.

- **Normal User**: Normal users are real people who are assigned a user account for login and limited access to computer applications, files, and resources. UID and GID = 1000 or greater, incremented with every new user.

- **System User**: A system user is typically a non-human or computer-generated account. System users are created to run a specific program, service, or process (daemon) such as a web server or backup program. This type of user is limited in control and assigned only enough access to manage its specific task. UID and GID are assigned from 1 to 999.

### `/etc/passwd`
---
Admin users can view and modify the user and group information stored in the read-only `/etc/passwd` file.

Example of the contents of a `/etc/passwd` file:

    ```
    root:x:0:0:root:/root:/bin/bash
    daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
    bin:x:2:2:bin:/bin:/usr/sbin/nologin
    sys:x:3:3:sys:/dev:/usr/sbin/nologin
    ...
    coder:x:2222:2222::/home/coder:/bin/bash
    ```

The user information is stored in the format of `username:password:user_id:group_id:GECOS:user_home_directory:user_shell`.

| Field                | Description                                                                                                                                                                                                                  |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Username**         | The name assigned to the user. It identifies the user in the system.                                                                                                                                                         |
| **Password**         | User's password status. An "x" means the password is stored in `/etc/shadow`. A "*" means the user does not have login access. A blank field means the user has no password set.                                           |
| **User ID (UID)**    | The unique numeric identifier for the user. It distinguishes the user in the system.                                                                                                                                        |
| **Group ID (GID)**   | The unique numeric identifier for the user's primary group. It links the user to their main group for permissions and access rights.                                                                                       |
| **GECOS**            | Used to store additional user information. This may include the user's full name, office location, phone number, or other identifying details.                                                                              |
| **Home Directory**   | The path to the user's home directory. This directory is the default location for the user's files and configurations.                                                                                                      |
| **Shell**            | The user's default shell. It specifies which command-line interface the user will use by default. Most users default to bash, but other shells can also be set.                                                             |

### User Management Commands
```zsh
    su
    sudo
    whoami
    useradd
```
---
1. **sudo** - Execute a command as another user, typically the superuser.
    ```zsh
    sudo command
    ```
    - Common flags: `-u` (specify the user to run as), `-s` (run a shell as the superuser)
    - Usage: `sudo apt-get update` - Run the `apt-get update` command as the superuser.
    - Usage: `sudo -u username command` - Run the specified command as the specified user.

2. **su** - Switch user.
    ```zsh
    su [username]
    ```
    - Common flags: `-` (start a login shell)
    - Usage: `su -` - Switch to the root user.
    - Usage: `su username` - Switch to the specified user.

3. **whoami** - Display the current user.
    ```zsh
    whoami
    ```
    - Usage: `whoami` - Print the username of the current user.

4. **useradd** - Create a new user.
    ```zsh
    useradd [options] username
    ```
    - Common flags: `-m` (create home directory), `-G` (add to group)
    - Usage: `useradd -m newuser` - Create a new user with a home directory.
    - Usage: `useradd -m -G groupname newuser` - Create a new user and add them to a group.


## User Groups
In Linux, user groups are a way to organize and manage users with similar access permissions. Groups simplify the management of user privileges by allowing permissions to be assigned to a group rather than to individual users. Each user can be a member of one or more groups, and each group can have specific rights to access files or execute commands.

### `/etc/group`
Admin users can view and modify tgroup information stored in the read-only `/etc/group` file.

Example of the contents of a `/etc/group` file:
    ```
    root:x:0:
    daemon:x:1:
    bin:x:2:
    sys:x:3:
    adm:x:4:ubuntu
    ...
    coder:x:2222:
    ```

### User Group Management Commands
```zsh
    groupadd
    groupdel
```
---
You can add or delete groups using the following commands:

1. **groupadd** - Create a new group.
    ```zsh
    groupadd groupname
    ```
    - Usage: `groupadd newgroup` - Create a new group called `newgroup`.

2. **groupdel** - Delete a group.
    ```zsh
    groupdel groupname
    ```
    - Usage: `groupdel oldgroup` - Delete the group `oldgroup`.


## Case Study
When containerizing our code to be sent to the server as jobs, it is crucial to follow best practices for security and permissions. One important aspect is to avoid running containers as the root user, which can lead to various security vulnerabilities and permission issues.

**Problem**: Running containers as the root user poses significant security risks. If a container running as root is compromised, it can potentially lead to unauthorized access to the host system. Additionally, running as root can cause permission conflicts, making it difficult to manage file and directory access within the container.

**Solution**: To mitigate these risks, we can create a non-root user within the container and change the ownership of the application files to this user. This approach enhances security by limiting the permissions of the containerized application and ensures that the application runs with the least privileges necessary.

```bash
ARG NON_ROOT_USER="aisg"
ARG NON_ROOT_UID="2222"
ARG NON_ROOT_GID="2222"
ARG HOME_DIR="/home/${NON_ROOT_USER}"

RUN useradd -l -m -s /bin/bash -u ${NON_ROOT_UID} ${NON_ROOT_USER}

# Set the non-root user as the default user
USER ${NON_ROOT_USER}

# Ensure the non-root user has the necessary permissions to access the copied files.
COPY --chown=${NON_ROOT_USER}:${NON_ROOT_GID} ${REPO_DIR} .

# Make the entrypoint script executable
RUN chmod +x ./entrypoint.sh

# Set the entrypoint
ENTRYPOINT ["./entrypoint.sh"]
```

- `useradd`: This command is used to create a new user. In this case, it creates a non-root user with the specified user ID (NON_ROOT_UID), group ID (NON_ROOT_GID), and home directory (HOME_DIR).

    * `-l`: Creates a system account.
    * `-m`: Creates the user's home directory if it does not exist.
    * `-s /bin/bash`: Sets the user's login shell to bash.
    * `-u ${NON_ROOT_UID}`: Specifies the user ID for the new user.

- `chown`: This command changes the ownership of files and directories. Here, it is used in the `COPY` instruction to set the ownership of the copied files to the newly created non-root user and group.

    * `--chown=${NON_ROOT_USER}:${NON_ROOT_GID}`: Sets the owner and group of the copied files to NON_ROOT_USER and NON_ROOT_GID.

- `chmod`: This command changes the file mode (permissions) of a file. In this case, it makes the entrypoint.sh script executable.

References:
- https://stackoverflow.com/questions/68155641/should-i-run-things-inside-a-docker-container-as-non-root-for-safety
- https://docs.docker.com/build/building/best-practices/
- https://www.redhat.com/en/blog/understanding-root-inside-and-outside-container