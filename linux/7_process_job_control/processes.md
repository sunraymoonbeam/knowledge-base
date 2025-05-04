---
title: 3. Processes
---
<div style="text-align: center;">
    <img src="https://i.ytimg.com/vi/nQhRRLgLFaQ/hq720.jpg?sqp=-oaymwEhCK4FEIIDSFryq4qpAxMIARUAAAAAGAElAADIQj0AgKJD&rs=AOn4CLB-IqxBRBnnzggkdUD64o4n_eVEiA" style="transform: scale(0.7);">
</div>

# Resources
1. https://linuxjourney.com/lesson/monitor-processes-ps-command


# Table of Contents
0. [Summary](#summary)
1. [Processes](#process)
    1. [Process Creation](#process-creation)
    2. [Process Termination](#process-termination)
    3. [Signals](#signals)
    4. [Process Management Commands](#process-management-commands)
2. [System Information Commands](#system-information-commands)


## Summary
---
| S/N | Command         | Description                                         | Example Usage                    |
| --- | ----------------| --------------------------------------------------- | -------------------------------- |
|     |                 | **Process Management**                              |                                  |
| 1   | `top`           | Displays real-time system processes                 | `top`                            |
| 2   | `ps`            | Displays running processes                          | `ps aux`                         |
| 3   | `kill`          | Terminates a process                                | `kill 1234`                      |
|     |                 | **System Information**                              |                                  |
| 4   | `df`            | Displays disk space usage                           | `df -h`                          |
| 5   | `du`            | Estimate and summarize file & directory space usage | `du -h -d 2 image/folder `       |
| 6   | `uname`         | Prints system information                           | `uname -mp`                      |
| 7   | `lscpu`         | Displays information about the CPU architecture     | `lscpu`                          |
| 8   | `lshw`     | Lists detailed information about hardware configs        | `lshw -short`                    |
| 9   | `lsblk`         | Lists information about block devices               | `lsblk -f`                       |


## Process
---
Everything in Linux is a file, even processes.

### Process Creation
---
TBC

### Process Termination
---
TBC

### Signals
---
TBC

### Process Management Commands
---
```zsh
    top
    ps
    kill
    lsof
```
---

1. **ps** - Display information about active processes.
    ```zsh
    ps
    ```
    - Common flags: `-aux` (show all processes)
    - Usage: `ps -aux` - Display detailed information about all running processes.

2. **top** - Display system processes.
    ```zsh
    top
    ```
    - Common flags: None
    - Usage: `top` - Display the system processes in real-time.

3. **kill** - Terminate processes.
    ```zsh
    kill PID
    ```
    - Common flags: `-9` (force kill)
    - Usage: `kill 1234` - Terminate the process with PID 1234.
    - Usage: `kill -9 1234` - Forcefully terminate the process with PID 1234.

4. **lsof** - List open files.
    ```zsh
    lsof
    ```
    - Common flags: `-i` (network files), `-u` (user), `-p` (process ID)
    - Usage: `lsof -i` - List all open network files.
    - Description: The `lsof`command lists information about files that are currently open by processes. It is useful for diagnosing issues related to file usage and network connections.


## System Information Commands
---
```zsh
    uname
    df
    du
    lscpu
    lshw
    lsblk
```
---

1. **uname** - Print system information.
    ```zsh
    uname
    ```
    - Common flags: `-a` (all information), `-r` (kernel release), `-s` (kernel name)
    - Usage 1: `uname -mp` - Print system architecture and processor information.
    - Usage 2: `uname -srv` - Print kernel name, kernel release and kernel version.
    - Usage 3: `uname -a` - Display all system information.
    - Description: The `uname` command prints information about the system, such as the kernel name, version, and machine hardware name.

2. **df** - Display disk space usage.
    ```zsh
    df
    ```
    - Common flags: `-h` (human-readable sizes)
    - Usage: `df -h` - Display disk space usage in human-readable sizes.

3. **du** - Estimate file space usage.
    ```zsh
    du
    ```
    - Common flags: `-h` (human-readable sizes), `-s` (summarize), `-a` (all files), `-d` (up to N levels deep)
    - Usage 1: `du -h` - Display disk usage in human-readable sizes.
    - Usage 2: `du -sh` - Summarize disk usage of the current directory in human-readable sizes.
    - Usage 3: `du -ah` - Display disk usage of all files and directories in human-readable sizes.
    - Description: The `du` command estimates and displays the disk space used by files and directories.

4. **lscpu** - Display information about the CPU architecture.
    ```zsh
    lscpu
    ```
    - Common flags: `--extended` (Displays information in a table)
    - Usage: `lscpu`- Display detailed information about the CPU architecture.
    - Description: The `lscpu` command gathers and displays information about the CPU architecture, including the number of CPUs, threads, cores, sockets, and more.

Output of ```lscpu```:
```
Architecture:             x86_64
CPU op-mode(s):         32-bit, 64-bit
Address sizes:          48 bits physical, 48 bits virtual
Byte Order:             Little Endian
CPU(s):                   16
On-line CPU(s) list:    0-15
Vendor ID:                AuthenticAMD
Model name:             AMD EPYC 7B12
    CPU family:           23
    Model:                49
    Thread(s) per core:   2
    Core(s) per socket:   8
```  

AISG's server has a total of 16 CPUs, with each physical core supporting 2 threads, resulting in 8 physical cores, and the system uses the x86_64 architecture. 

5. **lshw** - Lists detailed information about hardware configurations as root user.
    ```zsh
    lshw
    ```
    - Common flags: `-short` (short listing), `-html` (HTML output), `-json` (JSON output),  `-class` (filter by class, e.g., `-class disk`, `-class storage`)
    - Usage 1: `sudo lshw -short` - Display a short summary of hardware information in tabular format.
    - Usage 2: `sudo lshw -html > hardware.html` - Output hardware information in HTML format.
    - Usage 3: `sudo lshw -class disk -class storage -short` -  List all disks and storage controllers in tabular format.
    - Description: The `lshw` command provides detailed information about the hardware configuration of the machine, including memory, CPU, disks, and more.

6. **lsblk** - Lists information about devices.
    ```zsh
    lsblk
    ```
    - Common flags: `-f` (filesystems), `-d` (omit devices), `-l` (list format)
    - Usage 1: `lsblk` - Display information about all available block devices.
    - Usage 2: `lsblk -f` - Display information about filesystems.
    - Description: The `lsblk` command lists information about all available or specified block devices, showing their hierarchy and details such as size, type, and mount points.

Output of ```lsblk```:
```
NAME    MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda       8:0    0   300G  0 disk 
├─sda1    8:1    0 299.9G  0 part /etc/resolv.conf
│                                 /etc/hostname
│                                 /dev/termination-log
│                                 /etc/hosts
│                                 /etc/runai
├─sda14   8:14   0     4M  0 part 
└─sda15   8:15   0   106M  0 part 
sdb       8:16   0     8G  0 disk /home/coder
sdc       8:32   0     5G  0 disk 
```

