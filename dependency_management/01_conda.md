---
title: 1. conda
---


<div style="text-align: center;">
    <img src="https://miro.medium.com/v2/resize:fit:1200/1*VtuVP1_6jcLdTog1StekQQ.png" style="transform: scale(0.8);">
</div>

# Resources
1. https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html
2. https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-pkgs.html
3. https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-channels.html
4. https://docs.conda.io/projects/conda/en/latest/user-guide/concepts/channels.html


# Table of Contents
0. [Summary](#summary)
1. [What is Conda?](#introduction)
    1. [Environment & Package Manager](TBC)
    1. [Dependency Resolving](TBC)
2. [Conda Environment Management](#conda-environment-management)
3. [Conda Package Management](#conda-package-management)
    1. [Channels](#channels)
        1. [Channel Management Commands](#channel-management-commands)
    1. [*Package Management Commands*](#package-management-commands)
4. [Conda for Docker](#conda-containerization)
5. [Pros & Cons](#pros-and-cons)
    1. [The case for Conda](#the-case-for-conda)
    2. [The case against Conda](#the-case-against-conda)
    3. [Conda Alternatives](#conda-alternatives)


## Summary
---
| S/N | Command                                | Description                                         | Example Usage                                |
| --- | ---------------------------------------| --------------------------------------------------- | -------------------------------------------- |
|     |                                        | **Environment Management**                          |                                              |
| 1   | `conda create`                         | Create a new conda environment.                     | `conda create -n motextmotion python=3.10`   |
| 2   | `conda activate`                       | Activate an existing conda environment.             | `conda activate motextmotion`                |
| 3   | `conda deactivate`                     | Deactivate the current conda environment.           | `conda deactivate`                           |
| 4   | `conda env list`                       | List all conda environments.                        | `conda env list`                             |
| 5   | `conda env create`                     | Create an environment from an environment.yml file. | `conda env create -f environment.yml`        |
| 6   | `conda env update`                     | Update an environment from an environment.yml file. | `conda env update -f environment.yml --prune`|
| 7   | `conda remove -n myenv --all`          | Remove a conda environment.                         | `conda remove -n motextmotion --all`         |
| 8   | `conda env export -f environment.yml`  | Export an environment to a file.                    | `conda env export -f environment.yml`        |
|     |                                        | **Channel Management**                              |                                              |
| 9   | `conda config --add channels`          | Add a channel to the top of the channel list.       | `conda config --add channels conda-forge`    |
| 10  | `conda config --append channels`       | Append a channel to the end of the channel list.    | `conda config --append channels conda-forge` |
| 11  | `conda config --show-sources`          | Show configuration file sources used by Conda.      | `conda config --show-sources`                |
| 12  | `conda config --set channel_priority`  | Set priority for channel usage.                     | `conda config --set channel_priority strict` |
|     |                                        | **Package Management**                              |                                              |
| 13  | `conda install`                        | Install packages into the current environment.      | `conda install numpy scipy`                  |
| 14  | `conda install -c`                     | Install packages from a specific channel.           | `conda install -c conda-forge numpy`         |
| 15  | `conda update`                         | Update packages in the current environment.         | `conda update numpy`                         |
| 16  | `conda remove`                         | Remove packages from the current environment.       | `conda remove numpy`                         |
| 17  | `conda search`                         | Search for packages in conda repositories.          | `conda search conda-forge::numpy`            |
| 18  | `conda list --show-channel-urls`       | List all packages in the current environment.       | `conda list --show-channel-urls`             |


## Introduction
TBC


## Conda Environment Management
1. **conda create -n motextmotion** - Create a new conda environment called motextmotion.
    ```zsh
    conda create -n motextmotion
    ```
    - Common flags: `-n` (name of the environment), `-y` (automatic yes to prompts), `--platform` (specify the platform)
    - Usage 1: `conda create -n myenv python=3.9.12` - Create an environment named "myenv" with Python version 3.9.12.
    - Usage 2: `conda create -n myenv numpy pandas` - Create an environment named "myenv" with numpy and pandas packages.
    - Usage 3: `conda create -n myenv --platform osx-64 python` - Create an environment named "python-x64" with Python for the macOS 64-bit platform.

2. **conda activate motextmotion** - Activate an existing conda environment.
    ```zsh
    conda activate motextmotion
    ```
    - Common flags: None
    - Usage: `conda activate myenv` - Activate the environment named "myenv".

3. **conda deactivate** - Deactivate the current conda environment.
    ```zsh
    conda deactivate
    ```
    - Common flags: None
    - Usage: `conda deactivate` - Deactivate the currently active environment.

4. **conda env list** - List all conda environments.
    ```zsh
    conda env list
    ```
    - Common flags: None
    - Usage: `conda env list` - Display a list of all conda environments.

5. **conda env create** - Create an environment from an environment.yml file.
    ```zsh
    conda env create -f environment.yml
    ```
    - Common flags: `-f` (specify the environment file)
    - Usage: `conda env create -f environment.yml` - Create an environment from the specified environment.yml file.

6. **conda env update** - Update an environment from an environment.yml file.
    ```zsh
    conda env update -f environment.yml
    ```
    - Common flags: `-f` (specify the environment file), `--prune` (remove installed packages not defined in environment.yml)
    - Usage 1: `conda env update -f environment.yml` - Update the environment from the specified environment.yml file.
    - Usage 2: `conda env update -f environment.yml --prune` - Update the environment and remove any packages not specified in the environment.yml file.

7. **conda remove** - Remove a conda environment.
    ```zsh
    conda remove -n new-env --all
    ```
    - Common flags: `-n` (name of the environment), `--all` (remove all packages in the environment)
    - Usage: `conda remove -n motextmotion --all` - Remove the environment named "myenv" and all its packages.

8. **conda export** - Export an environment to a file.
    ```zsh
    conda env export -f environment.yml
    ```
    - Common flags: `--from-history` (export only explicitly installed packages), `-f` or `--file` (specify the file name or path)
    - Usage 1: `conda env export -f environment.yml` - Export the current environment to an environment.yml file.
    - Usage 2: `conda env export --from-history -f environment.yml` - Export only the explicitly installed packages to an environment.yml file.


## Conda Package Management

### Channels
Conda channels are the locations where packages are stored. They serve as the base for hosting and managing packages. Conda packages are downloaded from remote channels, which are URLs to directories containing conda packages. 

<div style="text-align: center;">
    <img src="https://training.nih-cfde.org/en/latest/General-Tools/Introduction-to-Conda/conda-imgs/conda-channel.png" style="transform: scale(0.8);">
</div>

#### Channel Management Commands
1. **conda config --add channels** - Add a channel to the top of the channel list.
   ```zsh
   conda config --add channels conda-forge
   ```
   - Common flags: `--add` (add a new channel)
   - Usage 1: `conda config --add channels conda-forge` - Adds `conda-forge` channel to the top of the list.

2. **conda config --append channels** - Append a channel to the end of the channel list.
   ```zsh
   conda config --append channels conda-forge
   ```
   - Common flags: `--append` (append a channel)
   - Usage 1: `conda config --append channels conda-forge` - Adds `conda-forge` to the end of the channel list.

3. **conda config --show-sources** - Show configuration file sources used by Conda.
   ```zsh
   conda config --show-sources
   ```
   - Common flags: `--show-sources` (show configuration sources)
   - Usage 1: `conda config --show-sources` - Lists all configuration file locations and sources Conda is using.

Output of `conda config --show-sources`:

    ```bash
    ==> /Users/lowrenhwa/.condarc <==
    default_python: None
    channels:
    - conda-forge
    - defaults
    ```

The .condarc file is a configuration file used by conda to manage various settings and preferences. 
This file allows users to customize their conda environment by specifying options such as default channels, package directories, and other configurations.

4. **conda config --set channel_priority** - Set priority for channel usage.
   ```zsh
   conda config --set channel_priority strict
   ```
   - Common flags: `--set` (set a configuration option)
   - Usage 1: `conda config --set channel_priority strict` - Sets default channel for pkg fetching (targets first channel in channel sources)
   - Usage 2: `conda config --set channel_priority flexible` - 

* With strict channel priority, packages in lower priority channels are not considered if a package with the same name appears in a higher priority channel.
* With flexible channel priority, the solver may reach into lower priority channels to fulfill dependencies, rather than raising an unsatisfiable error.

### Package Management Commands
1. **conda install** - Install packages into the current environment.
    ```zsh
    conda install <package>
    ```
    - Common flags: `-n` (name of the environment), `-y` (automatic yes to prompts), `--channel` or `-c` (specify the channel)
    - Usage 1: `conda install numpy scipy` - Install numpy and scipy packages into the current environment.
    - Usage 2: `conda install numpy=0.15.0` - Install a specific version (0.15.0) of the scipy package.
    - Usage 3: `conda install numpy>3.5,<4.0` - Install a version of numpy greater than 3.5 and less than 4.0. (AND logic)

2. **conda install -c CHANNELNAME PKGNAME** - Install packages from a specific channel.
    ```zsh
    conda install -c <channel> <package>
    ```
    - Common flags: `-c` (specify the channel)
    - Usage 1: `conda install numpy --channel conda-forge | conda install conda-forge::numpy` 
    - Usage 2: `conda install numpy --channel conda-forge --channel bioconda` - Priority decreases from left to right

3. **conda update** - Update packages in the current environment to the latest versions that are compatible with all other packages in the environment.
    ```zsh
    conda update <package>
    ```
    - Common flags: `-n` (name of the environment), `-y` (automatic yes to prompts), `--all` (update all packages)
    - Usage 1: `conda update numpy` - Update the numpy package to the latest version.
    - Usage 2: `conda update --all` - Update all packages in the current environment to the latest versions.

4. **conda remove** - Remove packages from the current environment. Will also remove **any package that depends on any of the specified packages** as well.
    ```zsh
    conda remove <package>
    ```
    - Common flags: `-n` (name of the environment), `-y` (automatic yes to prompts)
    - Usage: `conda remove numpy` - Remove the numpy package from the current environment.

5. **conda search** - Search for packages in conda repositories.
    ```zsh
    conda search <package>
    ```
    - Common flags: `--channel` or `-c` (specify the channel)
    - Usage 1: `conda search numpy` - Search for the scipy package in the default conda repositories.
    - Usage 2: `conda search -c conda-forge numpy | conda search conda-forge::numpy ` - Search for the scipy package in the default conda repositories.

6. **conda list** - List all packages in the current environment.
    ```zsh
    conda list
    ```
    - Common flags: `-n` (name of the environment), `--show-channel-urls`
    - Usage: `conda list --show-channel-urls` - List all installed packages in the current environment, along with the channels from which they were installed.


## Conda Containerization
We can use a base image `continuumio/miniconda3` that comes pre-installed with Miniconda.

```bash
FROM continuumio/miniconda3

ENV CONDA_ENV_NAME "motextmotion"

# Initialize conda for the current shell and create and activate the conda environment
RUN conda init bash && \
    conda env create -f ./environment.yml && \
    echo "conda activate ${CONDA_ENV_NAME}" >> ~/.bashrc
```

Then in the entrypoint script, simply activate the conda environment.

```bash
#!/bin/bash --login
set -e

# activate conda environment
conda activate motextmotion

exec "$@"
```


## Pros and Cons
TBC


### The case for Conda
TBC


### The case against Conda
TBC


### Conda Alternatives
Miniconda, Micro-Mamba