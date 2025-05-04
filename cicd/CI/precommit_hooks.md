---
title: 1. Pre-commit Hooks
---
<div style="text-align: center;">
    <img src="https://interrupt.memfault.com/img/pre-commit/cover.png" style="transform: scale(0.8);">
</div>

# Resources
1. https://pre-commit.com/
1. https://peps.python.org/pep-0008/
1. https://www.youtube.com/watch?v=hgI0p1zf31k&ab_channel=PythonDiscord
1. https://datarecipes.substack.com/p/7-must-have-pre-commit-hooks-for
1. https://motlin.medium.com/pre-commit-or-ci-cd-5779d3a0e566
1. https://www.marketcalls.in/python/a-comprehensive-guide-to-python-linters-pylint-black-and-ruff-explained.html
1. https://docs.astral.sh/ruff/


# Table of Contents
1. [Quick start: Installation and Usage](#installation-and-usage)
1. [Why Precommit Hooks?](#why-pre-commit-hooks)
1. [PEP 8 Convention](#pep-8)
   1. [Formatting](#formatting)
   1. [Linting](#linting)
   1. [Linting vs Formatting](#formatting-vs-linting)


## Installation and Usage
---
1. Install pre-commit with 
```bash
pip install pre-commit
```

2. Setup the git hooks by running the below command in the root of the project
```bash
pre-commit install
```

3. To set up pre-commit hooks for *commitizen*, an additional command has to be ran:
```bash
pre-commit install --hook-type commit-msg
```

To manually run the pre-commit hooks, you can use the command `pre-commit run --all-files`.

<details>
<summary>Chosen Pre-commit Hooks</summary>

```bash
repos:
- repo: https://github.com/pre-commit/pre-commit-hooks
  rev: v5.0.0
  hooks:
  - id: trailing-whitespace
  - id: end-of-file-fixer
  - id: check-json # Checks json files for parseable syntax.
  - id: check-yaml # Checks yaml files for parseable syntax.
  - id: check-toml # Check for parseable syntax in TOML files.
  - id: check-case-conflict # Check for files that would conflict in case-insensitive filesystems**
  - id: check-merge-conflict
  - id: check-added-large-files
    args: [--maxkb=50000]
    exclude: ^notebooks/
  - id: requirements-txt-fixer

- repo: https://github.com/astral-sh/ruff-pre-commit
  rev: v0.7.0
  hooks:
  - id: ruff
    types_or: [python, pyi]
    args:
    - --fix
    - --extend-select=I
    - --ignore=F401,F841
  - id: ruff-format

- repo: https://github.com/commitizen-tools/commitizen
  rev: v3.29.1
  hooks:
  - id: commitizen

- repo: https://github.com/Yelp/detect-secrets
  rev: v1.5.0
  hooks:
  - id: detect-secrets
```

| Hook Name                  | Function                                              | Justification                                      | Link                                                                 |
|----------------------------|-------------------------------------------------------|---------------------------------------------------|----------------------------------------------------------------------|
| trailing-whitespace        | Removes trailing whitespace                           | Keeps code clean and prevents unnecessary diffs   | [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)   |
| end-of-file-fixer          | Ensures files end with a newline                      | Prevents issues with POSIX compliance             | [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)   |
| check-json                 | Checks JSON files for parseable syntax                | Ensures JSON files are valid and parseable        | [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)   |
| check-yaml                 | Checks YAML files for parseable syntax                | Ensures YAML files are valid and parseable        | [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)   |
| check-toml                 | Checks TOML files for parseable syntax                | Ensures TOML files are valid and parseable        | [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)   |
| check-case-conflict        | Checks for files that conflict in case-insensitive filesystems | Prevents issues on case-insensitive filesystems | [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)   |
| check-merge-conflict       | Checks for merge conflict markers                     | Prevents committing unresolved merge conflicts    | [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)   |
| check-added-large-files    | Prevents adding large files                           | Keeps repository size manageable                  | [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)   |
| requirements-txt-fixer     | Sorts and deduplicates requirements.txt               | Keeps dependencies organized                      | [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)   |
| ruff                       | Lints and fixes Python code                           | Ensures code quality and consistency              | [ruff-pre-commit](https://github.com/astral-sh/ruff-pre-commit)      |
| ruff-format                | Formats Python code                                   | Ensures code is properly formatted                | [ruff-pre-commit](https://github.com/astral-sh/ruff-pre-commit)      |
| commitizen                 | Standardizes commit messages                          | Ensures consistent and meaningful commit messages | [commitizen](https://github.com/commitizen-tools/commitizen)         |
| detect-secrets             | Detects secrets in code                               | Prevents committing sensitive information         | [detect-secrets](https://github.com/Yelp/detect-secrets)             |
</details>

<details>
<summary>Pre-commit Hooks for future consideration</summary>

```bash
- repo: https://github.com/pdm-project/pdm
  rev: 2.15.0
  hooks:
    - id: pdm-lock-check
    - id: pdm-export
      # command arguments, e.g.:
      args: ['-o', 'requirements.txt', '--without-hashes']
      files: ^pdm.lock$

#  - repo: https://github.com/econchick/interrogate
#    rev: 1.7.0
#    hooks:
#       - id: interrogate
#         args: [-v, --fail-under=90, --exclude=notebooks]
#         pass_filenames: false  # needed if excluding files with pyproject.toml or setup.cfg
```
</details>


## Why Pre-commit Hooks?
[Pre-commit](https://pre-commit.com/) hooks are scripts that run before a commit is made. They are used to ensure that the code is **formatted correctly** and that there are **no errors** in the code.

In other words - pre-commit hooks help us to ensure that the code being pushed to our code base follows a certain standard or quality - and this typically follows a convention, such as pep8.

## PEP 8

<div style="text-align: center;">
    <img src="https://ioflood.com/blog/wp-content/uploads/2023/09/Python-script-adhering-to-PEP8-standards-checklist-formatted-code-Python-logo.jpg" style="transform: scale(0.8);">
</div>

What is pep8? To answer this, please watch the video at https://www.youtube.com/watch?v=hgI0p1zf31k&ab_channel=PythonDiscord first.

For those that are lazy, here is the lyrics:
```bash
You don't need any curly braces
just four spaces 
just four spaces

Wildcard imports should be avoided
in most cases
in most cases

Try to make sure there's no trailing whitespace
it's confusing
it's confusing

Trailing commas go behind list items
git blame tightens
git blame tightens

And comments are important
as long as they're maintained
when comments are misleading it will drive people insane
just try to be empathic
just try to be a friend 
it's really not that hard, just to adhere to

PEP 8
PEP 8

Constants should be named all capital letters
and live forever 
live forever

And camelCase is not for Python
never ever
never ever

And never use a bare exception 
be specific
be specific

No one likes the horizontal scrollbar 
keep it succinct
keep it succinct

And comments are important
as long as they're maintained 
when comments are misleading it will drive people insane 
just try to be empathic
just try to be a friend 
it's really not that hard just to adhere to

PEP8 
PEP8 
PEP8
PEP8
```
PEP 8 (Python Enhancement Proposal 8), sometimes spelled PEP8 or PEP-8, is a document that provides guidelines and best practices on how to write Python code.
This helps with:
* **Readability**: Code is read more often than it is written. PEP 8’s emphasis on consistent and readable code ensures that your codebase remains understandable to you and your team, even as it grows.

* **Maintainability**: Following PEP 8 guidelines simplifies the process of maintaining and updating your code. Consistent code style reduces the cognitive load when making changes.

* **Collaboration**: When working on open-source projects or with a team, adhering to PEP 8 ensures that everyone can easily contribute to and understand the codebase.

### Formatting
---
Formatting makes source code easier to read by human beings. By enforcing particular rules and conventions such as line spacing, indents, and spacing around operators, the code becomes more visually organized and comprehensible.

There are many formatting tools available, such as `pylint`, `pyflakes`, `black`, `flake8`, and `ruff`.

In our project, we choose to use `ruff`. Ruff is a Python linter and code formatter that focuses on being incredibly fast. It performs static analysis checks (like Pylint) but with a focus on speed and minimal resource usage. Ruff aims to cover a wide range of Python code quality checks, including linting, formatting, and detecting common issues in the codebase.

On their website, they state that: *Ruff can be used to replace Flake8 (plus dozens of plugins), Black, isort, pydocstyle, pyupgrade, autoflake, and more, all while executing tens or hundreds of times faster than any individual tool.*


<div style="text-align: center;">
    <img src="https://miro.medium.com/v2/resize:fit:1236/1*kpPu_N-od820mRhj34ZKWA.png" style="transform: scale(0.8);">
</div>


1. **`ruff format`** - Format Python code using Ruff.
    ```zsh
    ruff format
    ```
    - Common flags: `--check` (check if the code is formatted correctly without making changes)
    - Usage 1: `ruff format --check src` - Check if the code in the `src` directory is formatted correctly.

### Linting
---
Linting highlights semantic and stylistic problems in your Python source code, which often helps you identify and correct subtle programming errors or coding practices that can lead to errors. For example, linting can detect the use of an undefined variable, calls to undefined functions, missing parentheses, and even more subtle issues such as attempting to redefine built-in types or functions.

1. **`ruff check`** - Perform linting checks using Ruff.
    ```zsh
    ruff check
    ```
    - Common flags: `--extend-select` (extend the list of selected checks), `--ignore` (ignore specific checks)
    - Usage 1: `ruff check --extend-select="I" --ignore F401 --ignore F841 src` - Perform linting checks on the `src` directory, extending the selected checks and ignoring specific ones.

2. **`pylint`** - Perform linting checks using Pylint.
    ```zsh
    pylint src --fail-under=1.0 --ignore=tests --disable=W1202
    ```
    - Common flags: `--fail-under` (set a minimum score to pass), `--ignore` (ignore specific directories), `--disable` (disable specific checks)
    - Usage: `pylint src --fail-under=1.0 --ignore=tests --disable=W1202` - Perform linting checks on the `src` directory, ignoring the `tests` directory, and disabling specific checks.

The reason why we use Pylint in addition to Ruff is to obtain a quality score for our code. Pylint provides a detailed report with a score that helps us measure the quality of our codebase.

### Formatting vs Linting
---
* **Formatting**: Ensures that the code adheres to a consistent style, making it easier to read and understand. Tools like `ruff format` and `black` are used for formatting.

* **Linting**: Identifies potential errors and enforces coding standards. It helps catch bugs early and ensures that the code follows best practices. Tools like `ruff check` and `pylint` are used for linting.

Linting is distinct from Formatting because linting analyzes how the code runs and detects errors, whereas formatting only restructures how code appears.