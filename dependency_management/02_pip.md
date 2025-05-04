---
title: 2. pip
---




<div style="text-align: center;">
    <img src="https://img-b.udemycdn.com/course/750x422/3763810_5b93.jpg" style="transform: scale(0.8);">
</div>

# Resources
1. https://pip.pypa.io/en/stable/getting-started/
2. https://realpython.com/what-is-pip/#installing-packages-with-pip
3. https://realpython.com/python-wheels/
4. https://packaging.python.org/en/latest/discussions/package-formats/
5. https://pypi.org/


# Table of Contents
0. [Summary](#summary)
1. [Introduction to pip](#introduction)
1. [Python Packages](#python-packages)
    1. [Source Distribution](#source-distribution)
    1. [Binary Distribution - Wheels](#binary-distribution---wheels)
    1. [Source Distribution vs Binary Distribution](#source-distribution-vs-binary-distribution)
1. [Python Package Repositories - PyPi](#python-package-repositories---pypi)
1. [Dependency Resolving] (TBC)
1. [pip Package Management Commands](#pip-commands)
1. [Case Study - GCC Compiler and Source Distribution](TBC)

## Summary
---
| S/N | Command                                | Description                                              | Example Usage                                |
| --- | ---------------------------------------| -------------------------------------------------------- | -------------------------------------------- |
| 1   | `pip install <package>`                | Install packages into the current environment.           | `pip install numpy`                          |
| 2   | `pip uninstall <package>`              | Uninstall packages from the current environment.         | `pip uninstall numpy`                        |
| 3   | `pip check`                            | Verify installed packages have compatible dependencies.  | `pip check`                                  |
| 4   | `pip list`                             | List installed packages in the current environment.      | `pip list`                                   |
| 5   | `pip show <package>`                   | Display detailed information about a specific package.   | `pip show numpy`                             |
| 6   | `python setup.py sdist`                | Create a source distribution.                            | `python setup.py sdist`                      |
| 7   | `python setup.py bdist_wheel`          | Create a built distribution (wheel).                     | `python setup.py bdist_wheel`                |


## Introduction

### Python Packages

Python packages are collections of modules and other resources that are bundled together to provide specific functionality. These packages can be distributed and installed using package managers like `pip`. Python packages come in two primary forms: source distributions (`sdist`) and wheels (`.whl`).

#### Source Distribution
* Conceptually, a source distribution is an archive of the source code in raw form. Concretely, an sdist is a .tar.gz archive containing the source code plus an additional special file called `PKG-INFO`, which holds the project metadata.

* When `pip`, cannot find a wheel to install, it will fall back on downloading a source distribution, compiling a wheel from it, and installing the wheel. That is, the extension modules are compiled on the user's machine rather than the developer's. 

To build source distributions, use ```$ python setup.py sdist```

**python setup.py sdist** - Create a source distribution.
    ```zsh
    python setup.py sdist
    ```
     - Common flags: None
    - Usage: `python setup.py sdist` - Create a source distribution for the package.

#### Binary Distribution - Wheels
A wheel is a type of built distribution. In this case, built means that the wheel comes in a ready-to-install format and allows you to skip the build stage required with source distributions.

* Conceptually, a wheel contains exactly the files that need to be copied when installing the package. On the technical level, a wheel is a ZIP archive (unlike sdists which are TAR archives).

* There is a big difference between sdists and wheels for packages with extension modules, written in compiled languages like C, C++ and Rust, which need to be compiled into platform-dependent machine code. With these packages, wheels do not contain source code (like C source files) but compiled, executable code (like .so files on Linux or DLLs on Windows).

* Inside a wheel, you will find the package’s files, plus an additional directory called package_name-version.dist-info. This directory contains various files, including a METADATA file which is the equivalent of PKG-INFO in sdists, as well as RECORD. This can be useful to ensure no files are missing from your wheels.

To build wheel distributions, ```$ python setup.py bdist_wheel```

**python setup.py bdist_wheel** - Create a built distribution (wheel).
    ```zsh
    python setup.py bdist_wheel
    ```
    - Common flags: None
    - Usage: `python setup.py bdist_wheel` - Create a built distribution (wheel) for the package.

#### Source Distribution vs Binary Distribution
| Feature                    | sdist  | whl     |
|----------------------------|--------|---------|
| Contains source code       | Yes    | No      |
| Contains pre-compiled code | No     | Yes     |
| Installation speed         | Slower | Faster  |
| Requires build tools       | Yes    | No      |

* `pip` always prefers wheels over source distributions because wheels are faster to install. Even for pure-Python packages that do not contain any extension modules, wheels are still preferred because they streamline the installation process. Unlike source distributions, wheels contain pre-compiled extension modules, which means they do not need to be compiled on the user's machine. 

* This makes installation much faster for your users, since a wheel is directly installable. By only including files that must be installed, wheels also make for smaller downloads. It eliminates the need for a compiler and other build tools on the user's system.

* Furthermore, while there is only one sdist per version of a project, there may be many wheels. Again, this is most relevant in the context of extension modules. The compiled code of an extension module is tied to an operating system and processor architecture, and often also to the version of the Python interpreter (unless the Python stable ABI is used).


## Python Package Repositories - PyPi
Python Package Index (PyPi) is the most widely used repository for Python packages. It hosts thousands of packages that can be easily installed using `pip`. PyPi serves as a central repository where developers can publish their packages, and users can search for and install packages.

<div style="text-align: center;">
    <img src="https://cms.therecord.media/uploads/format_webp/pypl_d0123d805b.jpg" style="transform: scale(0.8);">
</div>

Start searching for packages on pypi by visiting https://pypi.org/ and typing in your desired package name in the search bar. In this case, we are looking for `numpy`.

![Screenshot_2024-11-05_at_4.08.55_PM](uploads/999676144e0c6af602ecb4811ff5c44d/Screenshot_2024-11-05_at_4.08.55_PM.png)

Here, we notice 2 seperate sections, the source distribution and the wheel distributions.

![Screenshot_2024-11-05_at_2.45.00_AM](uploads/135abe2fef2d572f422d3a0efac53844/Screenshot_2024-11-05_at_2.45.00_AM.png)

There exists many wheel distributions, seemingly compiled for different OS.

```bash
Source Distribution
numpy-2.1.2.tar.gz (18.9 MB view hashes)
Uploaded Oct 6, 2024 Source

Built Distributions
numpy-2.1.2-pp310-pypy310_pp73-win_amd64.whl (12.8 MB view hashes)
Uploaded Oct 6, 2024 PyPy Windows x86-64

numpy-2.1.2-pp310-pypy310_pp73-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (16.1 MB view hashes)
Uploaded Oct 6, 2024 PyPy manylinux: glibc 2.17+ x86-64
```

The source distribution (sdist) contains all the source code for the numpy package. This includes the original Python files, any C/C++ extension source files, and other necessary files like setup.py. When you install a package from a source distribution, the package is built (compiled) on your machine. This requires the necessary build tools and compilers to be present on your system.

Built distributions, or wheels (.whl files), are pre-compiled versions of the package. These wheels are specific to different operating systems and architectures., e.g. `win_amd64` for 64-bit Windows systems, ` manylinux` for  Linux systems with glibc 2.17 or newer, and `macosx` for macOS 14.0 or newer.


## Dependency Resolving



## pip commands
1. **pip install** - Install packages into the current environment.
    ```zsh
    pip install <package>
    ```
    - Common flags: `-r`(requirement), `-U`(upgrade), `--force-reinstall`, `-i` (index-url), `-f` (find-links)
    - Usage 1: `pip install -r requirements.txt` - Install from the given requirements file.
    - Usage 2: `pip install numpy=0.15.0 scipy` - Install numpy and scipy packages into the current environment.
    - Usage 3: `pip install -U -r requirements.txt` - Upgrade all specified packages to the newest available version.
    - Usage 4: `pip install --force-reinstall -r requirements.txt` - Reinstall all packages even if they are already up-to-date.
    - Usage 3: `pip install -i https://test.pypi.org/simple/ rptree` -  Install rptree from TestPyPI rather than from PyPI.

2. **pip uninstall** - Uninstall packages from the current environment.
    ```zsh
    pip uninstall <package>
    ```
    - Common flags: `-y` (automatic yes to prompts)
    - Usage 1: `pip uninstall numpy` - Uninstall the numpy package from the current environment.
    - Usage 2: `pip uninstall -y numpy` - Uninstall the numpy package without prompting for confirmation.

3. **pip check** - Verify installed packages have compatible dependencies.
    ```zsh
    pip check
    ```
    - Common flags: None
    - Usage: `pip check` - Check for broken dependencies in the current environment.

4. **pip list** - List installed packages in the current environment.
    ```zsh
    pip list
    ```
    - Common flags: `-o` (list outdated packages),  `-u` (list uptodate packages)
    - Usage 1: `pip list` - List all installed packages.
    - Usage 2: `pip list --outdated` - List all outdated packages..

5. **pip show** - Display detailed information about a specific package.
    ```zsh
    pip show <package>
    ```
    - Common flags: None
    - Usage: `pip show numpy` - Show detailed information about the numpy package.


## Case Study - Installing Hydra on Ubuntu (amd64, Python 3.7)
### Context
- Operating System: Ubuntu (x86_64 GNU/Linux)
- Python Version: 3.7
- Package: Hydra (for loading configurations)

### Problem
When attempting to install the Hydra package using pip install hydra, the installation failed. The error log indicated that the wheel for Hydra did not exist on PyPI for the specified platform and Python version (amd64, Python 3.7). As a result, pip attempted to download the source distribution (sdist) and compile it. However, the compilation process failed due to the absence of the GCC compiler.

![Screenshot_2024-11-05_at_12.41.55_PM](uploads/8721b557c4c89bb83fae0c09b426eadf/Screenshot_2024-11-05_at_12.41.55_PM.png)

### Error Log Analysis
Recall that pip typically tries to find a precompiled wheel for the specific Python and platform version. If a wheel is not available, pip falls back to downloading the source distribution (sdist) and attempts to compile it. The compilation process requires a C compiler, such as GCC, which was not installed on the system, leading to the installation failure.

### Solution
To resolve this issue, the following steps were taken:

1. Update the Package List and Upgrade Installed Packages
```bash
sudo apt-get update
sudo apt-get upgrade
```

2. Install Build-Essential Package
```bash
sudo apt-get install build-essential
```
The `build-essential package` is a meta-package that installs the GCC compiler, make, and other essential tools required for compiling software from source.

3. Install Hydra Using pip
```bash
pip install hydra
```
pip will now be able to compile the source distribution if a precompiled wheel is not available, completing the installation process successfully.
