---
title: 0. Cheatsheet
---

# Table of Contents
1. [Commands](#Summary)
    1. [Conda](#conda)
    2. [pip](#pip)
    3. [PDM](#pdm)

2. [Cheatsheets](#cheatsheets)

## Summary
### Conda
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

## pip
| S/N | Command                                | Description                                              | Example Usage                                |
| --- | ---------------------------------------| -------------------------------------------------------- | -------------------------------------------- |
| 1   | `pip install <package>`                | Install packages into the current environment.           | `pip install numpy`                          |
| 2   | `pip uninstall <package>`              | Uninstall packages from the current environment.         | `pip uninstall numpy`                        |
| 3   | `pip check`                            | Verify installed packages have compatible dependencies.  | `pip check`                                  |
| 4   | `pip list`                             | List installed packages in the current environment.      | `pip list`                                   |
| 5   | `pip show <package>`                   | Display detailed information about a specific package.   | `pip show numpy`                             |
| 6   | `python setup.py sdist`                | Create a source distribution.                            | `python setup.py sdist`                      |
| 7   | `python setup.py bdist_wheel`          | Create a built distribution (wheel).                     | `python setup.py bdist_wheel`                |

## PDM
| S/N | Command          | Description                                                                                                   |
|-----|------------------|---------------------------------------------------------------------------------------------------------------|
| 1   | `pdm init`       | Initializes a new PDM project, creating a new `pyproject.toml` file.                                          |
| 2   | `pdm add <pkg>`  | Adds a specified package (e.g., `torch`) to the project's dependencies and updates the `pyproject.toml` file. |
| 3   | `pdm lock`       | Generates a `pdm.lock` file that locks the dependencies of the project.                                       |
| 4   | `pdm sync`       | Syncs the project's dependencies with the `pyproject.toml` file, installing any missing packages.             |
| 5   | `pdm install`    | Installs the dependencies listed in the `pyproject.toml` file.                                                |
| 6   | `pdm remove <pkg>`| Removes a specified package from the project's dependencies.                                                 |
| 7   | `pdm export`     | Exports the dependencies to a different format, such as `requirements.txt`.                                   |


## Cheatsheets

<div style="text-align: center;">
    <img src="https://preview.redd.it/vp95ne1sq0k31.jpg?width=1920&format=pjpg&auto=webp&s=168c03ef4dae5ea5f5dbe9f913d0a634e4b044a3" style="transform: scale(0.99);">
</div>

---

<div style="text-align: center;">
    <img src="https://imgv2-1-f.scribdassets.com/img/document/500702883/original/f26c37ae5d/1726615882?v=1" style="transform: scale(0.99);">
</div>
