---
title: 4. Package Management
---
<div style="text-align: center;">
    <img src="https://www.linuxfordevices.com/wp-content/uploads/2021/03/ubuntu-Package-Manager.png" style="transform: scale(0.5);">
</div>

# Resources
1. https://www.linode.com/docs/guides/linux-package-management-overview/
2. https://www.linode.com/docs/guides/apt-package-manager
3. https://repolib.readthedocs.io/en/latest/deb822-format.html
4. https://www.linuxfordevices.com/tutorials/debian/apt-vs-dpkg-debian

# Table of Contents
0. [Cheatsheet](#summary)
1. [Packages](#packages)
2. [Package Repositories](#package-repositories)
3. [APT Package Manager](#apt-package-manager)
    1. [APT Commands](#apt-commands)
        1. [Upgrade and updating package information](#1-upgrade-and-updating-package-information)
        2. [Installing packages](#2-installing-packages)
        3. [Uninstalling packages](#3-uninstalling-packages)
        4. [Resource Management](#4-resource-management)
    2. [APT vs APT-GET](#apt-vs-apt-get)
4. [DPKG](#dpkg)
    1. [DPKG Commands](#dpkg-commands)
6. [Case Study- FFmpeg Installation in Coder Environment](#case-study)


## Summary
---
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
| 9   | `apt-get show`             | Displays information about a package               | `apt-get show curl`             |
| 10  | `apt-get search`          | Searches for packages                               | `apt-get search curl`            |
| 11  | `dpkg -i`                 | Installs a package                                  | `dpkg -i some_deb_package.deb`   |
| 12  | `dpkg -r`                 | Removes a package                                   | `dpkg -r some_deb_package.deb`   |
| 13  | `dpkg -l`                 | Lists installed packages                            | `dpkg -l`                        |


## Packages
On Linux, software is typically built as a package, distributed through repositories, and managed on the end-user’s system through package managers. Each Linux system typically contains thousands of packages, many of which are required dependencies for other packages.

Most software applications designed for Linux or Unix systems are distributed as packages, which are archives that contain the pre-compiled binary software files, installation scripts, configuration files, dependency requirements, and other details about the software. These packages are typically specific to a particular distribution and formatted in that distribution’s preferred package format, such as `.deb` for Debian/Ubuntu and `.rpm` for CentOS/RHEL.


## Package Repositories 
While a user can obtain package files through any method of file transfer, it is typical to use software repositories (also called repos) to obtain packages. Repositories are simply the location where the packages are stored, commonly accessible via the internet. A repository can contain a single package or thousands of packages (typically package dependecies).

These repositories are defined in the `/etc/apt/sources.list` file and additional files in the `/etc/apt/sources.list.d/` directory.

For example, you can view the contents of a repository file with the following command:

```bash
cat /etc/apt/sources.list.d/ubuntu.sources
```

Below is an example of what a `/etc/apt/sources.list` file looks like:

```bash
# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.

## Ubuntu distribution repository
##
## The following settings can be adjusted to configure which packages to use from Ubuntu.
## Mirror your choices (except for URIs and Suites) in the security section below to
## ensure timely security updates.
##
## Types: Append deb-src to enable the fetching of source package.
## URIs: A URL to the repository (you may add multiple URLs)
## Suites: The following additional suites can be configured
##   <name>-updates   - Major bug fix updates produced after the final release of the
##                      distribution.
##   <name>-backports - software from this repository may not have been tested as
##                      extensively as that contained in the main release, although it includes
##                      newer versions of some applications which may provide useful features.
##                      Also, please note that software in backports WILL NOT receive any review
##                      or updates from the Ubuntu security team.
## Components: Aside from main, the following components can be added to the list
##   restricted  - Software that may not be under a free license, or protected by patents.
##   universe    - Community maintained packages. Software in this repository receives maintenance
##                 from volunteers in the Ubuntu community, or a 10 year security maintenance
##                 commitment from Canonical when an Ubuntu Pro subscription is attached.
##   multiverse  - Community maintained of restricted. Software from this repository is
##                 ENTIRELY UNSUPPORTED by the Ubuntu team, and may not be under a free
##                 licence. Please satisfy yourself as to your rights to use the software.
##                 Also, please note that software in multiverse WILL NOT receive any
##                 review or updates from the Ubuntu security team.
##
## See the sources.list(5) manual page for further settings.
Types: deb
URIs: http://archive.ubuntu.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

## Ubuntu security updates. Aside from URIs and Suites,
## this should mirror your choices in the previous section.
Types: deb
URIs: http://security.ubuntu.com/ubuntu/
Suites: noble-security
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```

### DEB822 Source Format: 
* **Types**: Defines which types of packages to look for from a given source; either binary: `deb` or source code: `deb-src`. The deb type references a typical two-level Debian archive providing packages containing pre-compiled binary data intended for execution on user machines.

* **URIs**: The URLs from which the packages are downloaded. The URI must specify the base of the Debian distribution archive, from which APT finds the information it need.

* **Signed-By**: Ensures the authenticity and integrity of the packages by verifying them against a keyring file.

Read more here: https://repolib.readthedocs.io/en/latest/deb822-format.html


## APT Package Manager
A package manager simplifies the process for the end-user by automating the tasks of obtaining, installing, upgrading, and removing packages and their *dependencies*.

Different Linux distributions can have various components, and the choice of package manager can vary accordingly. At AISG, we use the Ubuntu distribution for our systems, which employs the APT (Advanced Package Tool) package manager, commonly used by Debian-based distributions like Ubuntu. If you were using RHEL, CentOS, or Fedora, you would use DNF/Yum.


### APT Commands

#### 1. Upgrade and updating package information
---
* **sudo apt-get update** - Update package information and upgrade all packages to their lastest versions.
```bash
sudo apt-get update && sudo apt-get upgrade
```

* ```sudo apt-get update``` downloads package information from all the sources/repositories configured on your system (within `/etc/apt/sources.list`). This command obtains details about the latest version for all available packages as well as their dependencies. 

* ```sudo apt-get upgrade``` then upgrades all packages to their latest versions, including upgrading existing dependencies and installing new ones.

#### 2. Installing packages
---
* **Install the specified package and all required dependencies.**
```bash
apt-get install [package]
```

* **Install a specific version of the specified package**
```bash
apt-get install [package]=[version]
```

* **Reinstall a package and any dependencies**
```bash
apt-get reinstall [package]
```

Useful if an installation for a package becomes corrupt or dependencies were somehow removed.


#### 3. Uninstalling packages
---
* **Removes the specified package from the system**
```bash
apt-get remove
```

* **Remove the package as well as any configuration files**
```bash
apt-get purge [package]
```

* **Remove any unused dependencies**
```bash
apt-get autoremove
```
Can sometimes help in reducing disk space (and clutter).

#### 4. Resource Management
---
* **List all packages that are installed**
```bash
apt-get list --installed
```

* **Displays information about an installed or available package.**
```bash
apt-get show [package]
```

* **Searches through all available packages for the specified package**
```bash
apt-get search [string]
```

#### APT vs APT-GET
`apt` : A newer end-user tool that consolidates the functionality of both apt-get and apt-cache.

`apt-get`: manages the installation, upgrades, and removal of packages (and their dependencies).

`apt-cache`: used to search for packages and retrieve details about a package.

In short, apt is a single tool that encompasses most of the functionality of other APT-specific tooling. Both Ubuntu and Debian recommend the apt command over apt-get and apt-cache. 


## DPKG
Dpkg (Debian Package Manager) **works under the hood of APT**. While APT manages remote repositories and resolves dependencies for you, it uses dpkg to actually make the changes of installing/removing packages. Dpkg on itself cannot retrieve/download files from remote repositories, nor can it figure out dependencies.

Read more here:  https://www.linuxfordevices.com/tutorials/debian/apt-vs-dpkg-debian

| Functions                                      | APT                | dpkg                                                   |
|------------------------------------------------|--------------------|--------------------------------------------------------|
| Can download packages from remote repositories | YES                | NO                                                     |
| Can resolve dependencies                       | YES                | NO                                                     |
| Install local packages                         | YES (using dpkg)   | YES                                                    |
| Install remote packages                        | YES (using dpkg)   | NO (users need to manually download a package if they wish to use dpkg) |
| List installed packages                        | YES                | YES                                                    |

### dkpg commands

* **Install a package**
```bash
dpkg -i some_deb_package.deb
```

* **Remove a package**
```bash
dpkg -r some_deb_package.deb
```

* **List installed packages**
```bash
dpkg -l
```

## More about keys and gnupg
TBC 

explain ```gnupg```

```bash
apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/3bf863cc.pub
```


## Case Study
**Context**: Motextmotion needs to render videos of animations, which requires the use of `FFmpeg`.

### About FFMPEG

<div style="text-align: center;">
    <img src="https://wsle.me/posts/uploads/img/FFmpeg_Logo.png" style="transform: scale(0.5);">
</div>

FFmpeg is a powerful multimedia framework that can decode, encode, transcode, mux, demux, stream, filter, and play almost anything that humans and machines have created. It supports the most obscure ancient formats up to the cutting edge. It is highly portable and can run on various operating systems, including Linux, macOS, and Windows.

### Solutions for Installing FFMPEG
#### **Solution 1**: Manual Installation Using APT

```bash
apt-get update
apt-get install ffmpeg
```

**Issue**: Since the coder environment is a container, once the container is stopped or removed, the installed packages will be lost. This means you would need to reinstall FFMPEG every time the container starts up, which is not efficient.

#### **Solution 2**: Automate Installation on Startup
To ensure FFMPEG is installed every time the container starts, you can add the installation commands to a startup script, such as `.bashrc` or another initialization script:

```bash
# Add to .bashrc or another startup script
apt-get update
apt-get install -y ffmpeg
```

**Issue**: While this ensures FFMPEG is installed on every startup, it can slow down the container initialization process and consume unnecessary resources by repeatedly downloading and installing the package.

#### **Solution 3**: Pre-install FFMPEG in the Docker Image
The most efficient solution is to include FFMPEG in the Docker image itself. This way, FFMPEG is pre-installed, and you do not need to install it every time the container starts. You can modify the Dockerfile to include the installation commands:

```bash
FROM your_base_image

# Install FFMPEG
RUN apt-get update && apt-get install -y ffmpeg
```

Benefit: This approach ensures that FFMPEG is always available in the container without the need for reinstallation, leading to faster startup times and more efficient resource usage.