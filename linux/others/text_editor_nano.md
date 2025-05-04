---
title: Nano Text Editor
---

# Text Editing with Nano

**Nano** is a simple, user-friendly text editor for Unix-like systems. It is often pre-installed on many Linux distributions and provides an easy way to create and edit text files directly from the terminal.

In many cases, a graphical user interface (GUI) is not available for editing files—for example, when running Docker containers. This makes learning terminal-based text editors, like Nano, valuable. Other alternatives include **vim** or **emacs**.

# Table of Contents
0. [Cheatsheet](#0-cheatsheet)  
1. [File Operations](#1-file-operations)  
2. [Navigation](#2-navigation)  
3. [Text Manipulation](#3-text-manipulation)  
4. [Searching](#4-searching)  
5. [Other Commands](#5-other-commands)  
6. [Example Workflow](#6-example-workflow)  

## 0. Cheatsheet
| S/N | Command          | Brief Description                                         |
|-----|------------------|-----------------------------------------------------------|
| 1   | `Ctrl + O`       | Write Out (Save) - Saves the current file.                |
| 2   | `Ctrl + X`       | Exit - Exits the Nano editor.                             |
| 3   | `Ctrl + A`       | Move to Beginning of Line - Moves cursor to start of line.|
| 4   | `Ctrl + E`       | Move to End of Line - Moves cursor to end of line.        |
| 5   | `Ctrl + Y`       | Page Up - Scrolls up one page.                            |
| 6   | `Ctrl + V`       | Page Down - Scrolls down one page.                        |
| 7   | `Ctrl + _`       | Go to Line - Jumps to a specific line number.             |
| 8   | `Alt + A`        | Set Mark - Marks text for selection.                      |
| 9   | `Ctrl + K`       | Cut Text - Cuts text and saves it to a buffer.            |
| 10  | `Ctrl + U`       | Uncut (Paste) Text - Pastes the text from the buffer.     |
| 11  | `Ctrl + J`       | Justify Text - Justifies the current paragraph.           |
| 12  | `Ctrl + W`       | Search - Search for text within the file.                 |
| 13  | `Ctrl + \`       | Search and Replace - Search for and replace text.         |
| 14  | `Ctrl + G`       | Get Help - Opens the help menu in Nano.                   |
| 15  | `Ctrl + C`       | Show Cursor Position - Displays the current cursor position.|
| 16  | `Ctrl + T`       | Spell Check - Performs spell checking.                   |
| 17  | `Alt + U`        | Undo - Undo the last action performed.                   |
---

## 1. File Operations

1. **`Ctrl + O`** - Write Out (Save)
```zsh
Ctrl + O # Prompts for a filename and saves the current content to that file.
```

2. **`Ctrl + X`** - Exit
```zsh
Ctrl + X # Exits Nano. If there are unsaved changes, Nano will prompt you to save them.
```
---

## 2. Navigation
1. **`Ctrl + A`** - Move to Beginning of Line
```zsh
Ctrl + A # Moves the cursor to the beginning of the current line.
```

2. **`Ctrl + E`** - Move to End of Line
```zsh
Ctrl + E # Moves the cursor to the end of the current line.
```

3. **`Ctrl + Y`** - Page Up
```zsh
Ctrl + Y # Moves up one page in the text.
```

4. `Ctrl + V` - Page Down
```zsh
Ctrl + V # Moves down one page in the text.
```

5. **`Ctrl + _`** - Go to Line
```zsh
Ctrl + _ # Prompts for a line number and moves the cursor to that line.
```
---

## 4. Text Manipulation

1. **`Ctrl + K`** - Cut Text
```zsh
Ctrl + K # Cuts the current line and stores it in the cut buffer. You can cut multiple lines by pressing `Ctrl + K` several times.
```

2. **`Ctrl + U`** - Uncut (Paste) Text
```zsh
Ctrl + U # Pastes the text from the cut buffer at the current cursor position.
```

3. **`Alt + A`** - Set Mark
```zsh
Alt + A # Sets a mark to select text. Use arrow keys to select text after setting the mark.
```

4. **`Ctrl + J`** - Justify Text
```zsh
Ctrl + J # Justifies the current paragraph.
```
---

## 4. Searching

1. **`Ctrl + W`** - Search
```zsh
Ctrl + W # Prompts for a search string and searches forward from the current cursor position. To move to the next match, press `Alt + W`.
```

2. **`Ctrl + \`** - Search and Replace
```zsh
Ctrl + \ # Prompts for a search string and a replacement string. It replaces occurrences of the search string with the replacement string. Press `A` to replace all matches.
```
---

## 5. Other Commands

1. **`Alt + U`** - Undo
```zsh
Alt + U # Undoes the last action performed in Nano.
```

2. **`Ctrl + C`** - Show Cursor Position
```zsh
Ctrl + C # Displays the current cursor position (line and column number).
```

3. **`Ctrl + G`** - Get Help
```zsh
Ctrl + G # Displays the help text with a list of all commands.
```
---

## 6. Example Workflow

1. **Opening a File for Search and Replace:**
    ```zsh
    nano example.txt
    ```
    - Opens the file `example.txt` for editing.

2. **Performing a Search and Replace:**
    - To replace the term "NaN" with "0":
        1. Press `Ctrl + \`.
        2. Enter "NaN" as the search string and press `Enter`.
        3. Enter "0" as the replacement string and press `Enter`.
        4. Nano will move to the first match and prompt for replacement. Press `Y` to replace, `N` to skip, or `A` to replace all matches.

3. **Navigating to a Specific Line:**
    - If you want to jump to line 50:
        1. Press `Ctrl + _`.
        2. Enter `50` and press `Enter`.

4. **Saving and Exiting:**
    - Once edits are complete:
        1. Press `Ctrl + O` to save changes.
        2. Press `Ctrl + X` to exit Nano.