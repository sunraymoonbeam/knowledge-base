---
title: File Compression & Archiving
---
<div style="text-align: center;">
    <img src="https://devconnected.com/wp-content/uploads/2019/11/featured-5.png" style="transform: scale(0.4);">
</div>

# Resources
1. https://linuxjourney.com/lesson/compressed-archives-tar
1. https://devconnected.com/how-to-archive-and-compress-files-on-linux/


# Table of Contents
0. [Summary](#Summary)
1. [Compression Methods](#Compression-methods)


## Summary
---
| S/N | Command  | Brief Description                                                                 | Example Usage                    |
|-----|----------|-----------------------------------------------------------------------------------|----------------------------------|
| 1   | `gzip`   | Compresses files using the GNU zip algorithm.                                     | `gzip file.txt`                  |
| 2   | `gunzip` | Decompresses files compressed with `gzip`.                                        | `gunzip file.txt.gz`             |
| 3   | `tar`    | Archives multiple files into a single file. Often used with compression utilities.| `tar -cvf archive.tar file1 file2`|
| 4   | `bzip2`  | Compresses files using the Burrows-Wheeler block sorting text compression algorithm.| `bzip2 file.txt`                |
| 5   | `bunzip2`| Decompresses files compressed with `bzip2`.                                       | `bunzip2 file.txt.bz2`           |
| 6   | `compress`| Compresses files using the Lempel-Ziv coding (LZW) algorithm.                    | `compress file.txt`              |
| 7   | `uncompress`| Decompresses files compressed with `compress`.                                 | `uncompress file.txt.Z`          |
| 8   | `zip`    | Compresses files into a ZIP archive.                                              | `zip archive.zip file1 file2`    |
| 9   | `unzip`  | Decompresses files from a ZIP archive.                                            | `unzip archive.zip`              |


## Compression methods
---
### gzip
`gzip` is a popular file compression utility that uses the GNU zip algorithm. It is commonly used in Unix and Linux systems to reduce the size of files for storage or transmission.

### gunzip
`gunzip` is the decompression counterpart to `gzip`. It is used to decompress files that were compressed using `gzip`.

### tar
`tar` stands for "tape archive" and is used to archive multiple files into a single file. While `tar` itself does not compress files, it is often used in conjunction with compression utilities like `gzip` or `bzip2`.

### bzip2
`bzip2` is a compression utility that uses the Burrows-Wheeler block sorting text compression algorithm. It typically achieves better compression ratios than `gzip`.

### bunzip2
`bunzip2` is the decompression counterpart to `bzip2`. It is used to decompress files that were compressed using `bzip2`.

### compress
`compress` is a file compression utility that uses the Lempel-Ziv coding (LZW) algorithm. It is less commonly used today but was popular in earlier Unix systems.

### uncompress
`uncompress` is the decompression counterpart to `compress`. It is used to decompress files that were compressed using `compress`.

### zip
`zip` is a widely-used file compression and packaging utility. It can compress multiple files into a single ZIP archive, which can then be easily shared or stored.

### unzip
`unzip` is the decompression counterpart to `zip`. It is used to extract files from a ZIP archive.