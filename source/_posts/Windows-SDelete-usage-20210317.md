---
title: Windows SDelete 清理未使用空间
date: 2021-03-17 21:09:23
tags: [Windows]
categories:
---







Usage: 

```
sdelete [-p passes] [-r] [-s] [-q] <file or directory> [...]
sdelete [-p passes] [-z|-c [percent free]] <drive letter [...]>
sdelete [-p passes] [-z|-c] <physical disk number>
```



| Parameter     | Description                                                  |
| :------------ | :----------------------------------------------------------- |
| **-c**        | Clean free space. Specify an option amount of space to leave free for use by a running system. |
| **-p**        | Specifies number of overwrite passes (default is 1).         |
| **-r**        | Remove Read-Only attribute.                                  |
| **-s**        | Recurse subdirectories.                                      |
| **-z**        | Zero free space (good for virtual disk optimization).        |
| **-nobanner** | Do not display the startup banner and copyright message.     |





SDelete is a free command line tool that you can use to delete files securely so that they cannot be recovered anymore.

The program overwrites the data on the hard drive directly when it is run to prevent file recovery software from picking up traces of deleted files and data.

The application has two main use cases: delete files securely to prevent recovery, or overwrite free disk space securely to delete traces of previously deleted files on it.

You can download [SDelete](https://docs.microsoft.com/en-us/sysinternals/downloads/sdelete) from the Microsoft website. You find a detailed article on it and its use as well. Extract the archive to your system. Since SDelete is a command line tool, you need to run it from the command line.

One easy option to open the SDelete folder on the command line is to open it in Windows Explorer, type cmd in the Explorer address field, and hit the Enter-key. This should open a command prompt at that location.

**Deleting files or folders securely**

The command `sdelete <file or directory>` is the base command for any file or directory operations. Here are some examples that demonstrates the different options that you have:

- `sdelete -s c:\users\martin\downloads` -- This deletes the folder downloads and all subdirectories securely.
- `sdelete -q c:\users\martin\text.txt` -- This deletes the file text.txt securely, and suppresses any error messages.
- `sdelete -p 5 c:\*.zip` -- This deletes all zip files under c:, and runs the operation in five passes.

**Deleting free disk space securely**

The base command `sdelete -c <drive letter>`  deletes free disk space on the specified drive securely. Here are some examples that demonstrate the various options that SDelete offers:

- `sdelete -c c:` -- Deletes the free disk space on drive C: securely.
- `sdelete -c -p 2 f:` -- Runs two delete passes on the free disk space of drive f:
- `sdelete -z d: `-- Zeroes the free disk space of drive d:

SDelete uses the Department of Defense algorithm by default when it deletes files, folders or free disk space. The `-z` parameter overwrites the free disk space with zeroes instead which is faster.

The operations may take quite some time to complete. The length it takes depends on the selection. Deleting individual files securely is pretty fast, but it may take hours or even longer if you run the operation on large folders or free space of entire hard drives.

It is recommended to run recovery software afterwards on the path to verify that the files cannot be recovered anymore.





