---
title: Basic Commands
---
<div style="text-align: center;">
    <img src="https://media.licdn.com/dms/image/D5612AQGd-Va5Jsa57g/article-cover_image-shrink_600_2000/0/1681635902710?e=2147483647&v=beta&t=4xhKrwCXKBmglW5pFQ1a7B02vh3GzJMJXd9zDoBl-lg" style="transform: scale(0.7);">
</div>

# Resources
1. https://linuxjourney.com/lesson/the-shell
2. https://www.freecodecamp.org/news/the-linux-commands-handbook/


# Table of Contents
0. [Summary](#Summary)
1. [File Management](#1-file-management)
1. [Directory Management](#2-directory-management)
1. [File Viewing](#3-file-viewing)
1. [Text / Output Processing](#4-text--output-processing)
1. [Environment Management](#5-environment-management)
1. [Pipes](#pipes)
1. [Others](#others)


## Summary
---
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


## 1. File Management
```zsh
    touch filename
    cp source destination
    mv source destination
    rm filename
```
---
1. **touch** - Create an empty file or update the timestamp of an existing file.
    ```zsh
    touch filename
    ```
    - Common flags: None
    - Usage: `touch newfile.txt` - Create a new file called "newfile.txt".

2. **cp** - Copy files or directories.
    ```zsh
    cp source destination
    ```
    - Common flags: `-r` (recursive for directories), `-i` (interactive, prompts before overwriting), `-t` (specify the target directory explicitly), `-v` (verbose, shows the files being copied)
    - Usage: `cp file1.txt file2.txt` - Copy file1.txt and name the copy file2.txt.
    - Usage with `-r`: `cp -r dir1/ dir2/` - Copy the contents of dir1 to dir2.
    - Usage with `-t`: `cp -t target_dir file1.txt file2.txt` - Copy `file1.txt` and `file2.txt` into `target_dir`.

3. **mv** - Move or rename files or directories.
    ```zsh
    mv source destination
    ```
    - Common flags: `-i` (interactive), `-b` (backup)
    - Usage: `mv oldname.txt newname.txt` - Rename the file oldname.txt to newname.txt.
    - Usage with `-b`: `mv -b file1.txt file2.txt` - Move file1.txt to file2.txt, creating a backup of file2.txt if it exists.
    - Usage with `-i`: `mv -i file1.txt file2.txt` - Move file1.txt to file2.txt, prompting for confirmation if file2.txt exists.

4. **rm** - Remove files or directories. Note that only empty directories can be removed with `rm`.
    ```zsh
    rm filename
    ```
    - Common flags: `-r` (recursive for directories), `-f` (force) -> please be careful when using this.
    - Usage: `rm file.txt` - Remove the file file.txt.


## 2. Directory Management
```zsh
    pwd
    cd
    ls
    mkdir
    rmdir
```
---
5. **pwd** - Print working directory.
    ```zsh
    pwd
    ```
    - Common flags: None
    - Usage: `pwd` - Print the current working directory.

6. **cd** - Change directory.
    ```zsh
    cd directory
    ```
    - Common flags: None
    - Usage: `cd /path/to/directory` - Change the current directory to /path/to/directory.
    - Usage: `cd ..` - Navigate up one level in the directory hierarchy (parent directory). To go up two levels, use `cd ../..`.
    - Usage: `cd -` - Change the current directory to the previous most recent directory.

7. **ls** - List directory contents.
    ```zsh
    ls
    ```
    - Common flags: `-l` (long format), `-a` (all files including hidden), `-h` (human-readable sizes), `t` (sorted by the [t]ime the file was modified)
    - Usage: `ls -lhat` - List all files and directories in long format with human-readable sizes.

8. **mkdir** - Create a new directory.
    ```zsh
    mkdir directory
    ```
    - Common flags: `-p` (create parent directories as needed)
    - Usage: `mkdir newdir` - Create a new directory called newdir.
    - Usage with `-p`: `mkdir -p parent/child` - Create the directory child inside the directory parent, creating parent if it doesn't exist.

9. **rmdir** - Remove an empty directory.
    ```zsh
    rmdir directory
    ```
    - Common flags: None
    - Usage: `rmdir olddir` - Remove the empty directory called olddir.


## 3. File Viewing
```zsh
    cat filename
    file filename
    open filename
    less filename
    head file
    tail file
```
---
10. **cat** - Concatenate and display file content.
    ```zsh
    cat filename
    ```
    - Common flags: `-n` (number lines)
    - Usage 1: `cat file.txt` - Display the content of file.txt.
    - Usage 2: `cat file1 file2 >> file3` - Concatenate  the  content  of  multiple  files  into  a  new file

11. **file** - Determine file type.
    ```zsh
    file filename
    ```
    - Common flags: None
    - Usage: `file file.txt` - Determine the type of file.txt.

12. **less** - View file content one screen at a time.
    ```zsh
    less filename
    ```
    - Common flags: `-N` (show line numbers), `-S` (chop long lines)
    - Usage: `less file.txt` - View the content of file.txt one screen at a time.

13. **head** - Output the first part of files.
    ```zsh
    head file
    ```
    Common flags: -n (number of lines)
    Usage: `head -n 10 file.txt` - Display the first 10 lines of file.txt.

14. **tail** - Output the last part of files.
    ```zsh
    tail file
    ```
    Common flags: `-n` (number of lines), `-f `(follow the file as it grows)
    Usage 1: `tail -n 10 file.txt` - Display the last 10 lines of file.txt.
    Usage 2: `tail -f file.txt` - Follow the file as it grows, useful for logs.


## 4. Text / Output Processing
```zsh
    sort file
    uniq file
    find path -name pattern
    grep pattern file
    wc file
    nl file
```
15. **sort** - Sort lines of text files.
    ```zsh
    sort file
    ```
    - Common flags: `-r` (reverse), `-n` (numeric sort), `-u` (unique sort, removes duplicates),  `-k` (sort by specific column)
    - Usage: `sort file.txt` - Sort the lines in file.txt alphabetically.

16. **uniq** - Report or omit repeated lines.
    Note that `uniq` only works on sorted files, repeated lines must be adjacent. Thus, it is often used in conjunction with `sort`.
    ```zsh
    uniq file
    ```
    - Common flags: `-c` (count), `-d` (only duplicates), `-u` (only unique)
    - Usage 1: `sort file.txt | uniq` - Remove duplicate lines from file.txt.
    - Usage 2: `sort file.txt | uniq -c` - Count the occurrences of each line in file.txt.
    - Usage 3: `sort file.txt | uniq -dc` - Display only the duplicate lines in file.txt, along with their number of occurences.
    - Usage 4: `sort file.txt | uniq -u` - Display only the unique lines in file.txt (the lines that only appear exactly once in the file).

17. **find** - Search for files in a directory hierarchy recursively.

    Note that the `find` command is **recursive** by default. It searches through the specified directory and all its subdirectories unless you limit the depth of the search using the `-maxdepth` option.

    ```zsh
    find path -name pattern
    ```
    - Common flags: `-name` (search by name), `-type` (search by type), `-maxdepth` (limit search depth), `-size` (search by file size)
    - Usage 1: `find . -name "*.txt"` - Search for files with the .txt extension in the current directory and all subdirectories.
    - Usage 2: `find . -type d -name "dirname"` - Search for directories with names matching "dirname".
    - Usage 3: `find . -maxdepth 1 -name "*.txt"` - Search for files with the .txt extension in the current directory only (one level deep).
    - Usage 4: `find . -size +100M` - Search for files larger than 100 megabytes.

18. **grep** - Search text using (regex) patterns.
    ```zsh
    grep pattern path/to/file
    ```
    - Common flags: `-r` (recursive), `-i` (ignore case), `-n` (line number), `-c` (count matches)
    - Usage 1: `grep "search_term" file.txt` - Search for the "search_term" in file.txt.
    - Usage 2: `grep "search_term" linux/*.txt` - Search for the "search_term" in all .txt files in the linux directory.
    - Usage 3: `grep -n -C 2 "search_term" file.txt` - Display the line number and 2 lines before and after each match.
    - Usage 4: `grep "search.*term" file.txt` - Search for any text that starts with "search" and ends with "term" with any characters in between in file.txt.

19. **wc** - Count lines, word, or bytes/characters.
    A line is defined as a sequence of characters ending with a newline character (`\n`). A word is defined as a sequence of characters separated by whitespace (spaces, tabs, or newlines). A byte is defined as a sequence of 8 bits, and a character is defined as a single unit of text (which may be more than one byte in some encodings).
    ```zsh
    wc file
    ```
    - Common flags: `-l` (lines), `-w` (words), `-c` (characters / bytes)
    - Usage 1: `wc file.txt` - Display the number of lines, words, and bytes in file.txt.
    - Usage 2: `wc -l file.txt` - Display the number of lines in file.txt.
    - Usage 3: `find . -maxdepth 1 -name "*.png" | wc -l` - Count the number of `.png` files in the current directory.

20. **nl** - Number lines of files.
    ```zsh
    nl file
    ```
    - Common flags: None
    - Usage 1: `nl file.txt` - Display the number of lines in file.txt.
    - Usage 2: `find . -maxdepth 1 -name "*.png" | nl` - Number and list the `.png` files in the current directory.


## 5. Environment Management
In computer operating systems, an environment is a collection of settings and information that defines the behavior of programs and applications. This environment includes various parameters that can influence how software operates, such as paths to executable files, user preferences, and system configurations.

Linux environment variables are key-value pairs used by applications to obtain information about the environment in which they are running. Each environment variable has a unique name and an associated value. These variables can control various aspects of the system's behavior and are essential for the proper functioning of many applications.

Environment variables can be accessed and referenced using the `$` symbol followed by the variable name. For example, `$HOME` refers to the current user's home directory. Some other common environment variables include `$PATH`, which specifies the directories where executable files are located, and `$USER`, which contains the name of the current user.

By setting and modifying environment variables, users and administrators can customize the behavior of the operating system and applications to suit their needs.

```zsh
    echo $VARIABLE
    export VARIABLE=value
    env VARIABLE=value command
    printenv
    unset VARIABLE
```
---
1. **echo** - Display a line of text or the value of a variable.
    ```zsh
    echo [option] [string]
    ```
    - Common flags: `-n` (do not output the trailing newline)
    - Usage 1: `echo "Hello, World!"` - Print "Hello, World!" to the terminal.
    - Usage 2: `echo $VARIABLE` - Print the value of the VARIABLE environment variable.

2. **export** - Set environment variables.
    ```zsh
    export VARIABLE=value
    ```
    - Common flags: None
    - Usage: `export PATH=/usr/local/bin` - Set the PATH environment variable to /usr/local/bin.
    - Usage: `export VARIABLE=value` - Set the VARIABLE environment variable to value.

3. **env** - Display or set environment variables.
    ```zsh
    env
    ```
    - Common flags: None
    - Usage 1 : `env` - Display all environment variables.
    - Usage 2: `env VARIABLE=value command` - Set the VARIABLE environment variable for the duration of the command.

4. **printenv** - Print environment variables.
    ```zsh
    printenv
    ```
    - Common flags: None
    - Usage: `printenv` - Print the values of all environment variables.

5. **unset** - Delete environment variables.
    ```zsh
    unset VARIABLE
    ```
    - Common flags: None
    - Usage: `unset VARIABLE` - Remove the VARIABLE environment variable.


## Pipes
![image](https://linuxhandbook.com/content/images/2020/09/pipe-redirection.png)

- **Pipe (`|`)** - Used to pass the output of one command as input to another. 
The pipe command, denoted by the bar “|”, is a powerful tool in Linux and other Unix-like operating systems that allows you to connect the output of one command to the input of another. 
Consider it a pipe that takes the data flowing out of one program and feeds it directly into the next program in the sequence. 
This lets you chain multiple commands to perform complex tasks in a single line.

Pipes are usually used for simple filtering and sorting, but they also can be used for data transformation, conditional execution, file processing and custom scripting.

1. **Searching for a specific environment in conda:**
    ```zsh
    conda env list | grep "env_name"
    ```
    - This command lists all conda environments and then searches for "env_name" within that list. It's useful when you have many environments and want to quickly find a specific one.

2. **Counting the number of files in the system:**
    ```zsh
    find /path/to/search -type f | wc -l
    ```
    - This command finds all files (`-type f`) in the specified path and then pipes the output to `wc -l`, which counts the number of lines. Each line represents a file, so this effectively counts the total number of files.

3. **Finding and displaying large files:**
    ```zsh
    find /path/to/search -type f -size +100M | xargs ls -lh
    ```
    - This command finds all files larger than 100MB and then pipes the list of files to `ls -lh` using `xargs`, which displays detailed information about each file, including size in a human-readable format.

4. **Filtering processes by name:**
    ```zsh
    ps aux | grep "process_name"
    ```
    - This command lists all running processes and then filters the list to show only those that match "process_name". It's useful for monitoring or managing specific processes.

5. **Extracting specific columns from a file:**
    ```zsh
    cat file.txt | awk '{print $1, $3}'
    ```
    - This command displays the contents of `file.txt` and then uses `awk` to extract and print the first and third columns of each line. It's useful for processing structured text data.


## Others
```zsh
    which
    alias
    tee
    history
    clear
    man
    time
```
1. **which** - Locate a command.
    ```zsh
    which command
    ```
    - Common flags: None
    - Usage: `which python` - Locate the path of the `python` executable.
    - Description: The [`which`] command shows the full path of shell commands. It searches for the command in the directories listed in the `PATH` environment variable.
    - **PATH Variable**: The `PATH` variable is an environment variable that specifies a list of directories where executable programs are located. When you type a command, the shell searches these directories in order to find the executable file.

2. **alias** - Create an alias for a command.
    ```zsh
    alias name='command'
    ```
    - Common flags: None
    - Usage: `alias ll='ls -la'` - Create an alias `ll` for the command `ls -la`.
    - Description: The `alias` command allows you to create shortcuts for longer commands. This can save time and reduce typing errors.

To remove an alias, use `unalias`.

3. **tee** - Read from standard input and write to standard output and files.
    ```zsh
    tee [options] [file...]
    ```
    - Common flags: `-a` (append to the given files, do not overwrite)
    - Usage 1: `echo "Hello, World!" | tee file.txt` - Write "Hello, World!" to both the terminal and `file.txt`.
    - Usage 2: `echo "Hello, World!" | tee -a file.txt` - Append "Hello, World!" to `file.txt` and also display it in the terminal.
    - Description: The `tee` command reads from standard input and writes to standard output and one or more files. It is useful for logging or splitting output.

4. **history** - Display the command history.
    ```zsh
    history
    ```
    - Common flags: `-c` (clear the history), `-w` (write the history to the history file)
    - Usage: [`history`] - Display the list of previously executed commands.
    - Description: The [`history`] command shows the list of commands that have been entered in the current shell session. It is useful for recalling and re-executing previous commands.

5. **clear** - Clear the terminal screen.
    ```zsh
    clear
    ```
    - Common flags: None
    - Usage: `clear` - Clear the terminal screen.
    - Description: The `clear`command clears the terminal screen, removing all previous commands and output. It is useful for decluttering the terminal.

6. **man** - Display the manual for a command.
    ```zsh
    man command
    ```
    - Common flags: None
    - Usage: `man ls` - Display the manual for the `ls` command.
    - Description: The `man` command is used to display the manual pages for other commands. It provides detailed information about the command's usage, options, and examples.

7. **time** - Measure the duration of command execution.
    ```zsh
    time command
    ```
    - Common flags: None
    - Usage: `time ls` - Measure the time it takes to execute the `ls` command.
    - Description: The `time` command runs the specified command and displays the amount of time taken to execute it. It provides three times: real (total elapsed time), user (time spent in user mode), and sys (time spent in kernel mode).
