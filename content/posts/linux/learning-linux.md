---
title: "Learning Linux"
date: 2020-06-11T21:25:13+08:00
draft: false
toc: true
mathjax: false
tags:
  - linux
---

## 1. Basic commands

Navigating the filesystem & handling directories and files

|name  |      description|
|------|-----------------|
|cd    | change directory|
|ls    |             list|
|touch |  create new file|
|cp    |             copy|
|ln    |          linking|
|mv    |             move|
|rm    |           remove|
|mkdir | create directory|

View the contents of files

|name|                    description|
|----|-------------------------------|
|file|            determine file type|
|cat | view entire contents of a file|
|more| view entire contents of a file|
|less| view entire contents of a file|
|head|       view top lines of a file|
|tail|    view bottom lines of a file|

Monitoring programs and disk space

|name |                         description|
|-----|------------------------------------|
|ps   |                     display process|
|top  |                     display process|
|kill |             send signals to process|
|mount|               mount storage devices|
|df   | report file system disk space usage|
|du   |           estimate file space usage|

Working with data files

|name |               description|
|-----|--------------------------|
|sort |                sort lines|
|grep | pattern matching by lines|
|tar  |                   archive|

Command-related

|name   |                      description|
|-------|---------------------------------|
|man    |           show reference manuals|
|which  |    show the full path of command|
|type   | show description of command type|
|history|             show command history|
|alias  |        define or display aliases|

## 2. Shell

|name                |                                         description|
|--------------------|----------------------------------------------------|
|command1; command2  |                  execute commands one after another|
|(command1; command2)| process list:create a subshell and execute commands|
|command &           |                       execute command in background|
|jobs                |                     display processes in background|
|coproc command      | create a subshell in background and execute command|

## 3. Environment variables

Linux environment variables help to store system information,
temporary data and configuration information.

Define a environment variable.

```bash
variable_name=variable_value
echo $variable_name
```

Define a global environment variable.

```bash
variable_name=variable_value
export variable_name
echo $variable_name
```

Add new path.

```bash
PATH=$PATH:/your/new/path
```

Define an array variable.

```bash
array_name=(element1 element2 element3)
array_name[1]=element4
echo ${array_name[0]}
echo ${array_name[*]}
```

## 4. File permissions

The permission of each file is in form of `-rwxrwxrwx`.
The first character defines the type of the file.

* \- for files
* d for directories
* l for links
* c for character devices
* b for block devices
* n for network devices

After that define the permissions of the owner, the group owning the file,
and everyone else.

|name |                 description|
|-----|----------------------------|
|chmod|        change file mod bits|
|chown| change file owner and group|

## 5. Filesystem

The default filesystem of linux is ext filesystem.
The ext filesystem uses an inode system to create a **inode table**,
to store file information,
including filename, file size, the owner & group,
file permission and pointers to disk block.

The ext4 filesystem, the newest ext filesystem,
is a **journaling filesystem**.
It write file changes into a temporary file first.
After data is successfully written to the storage device
and the inode table is updated,
the journal file is deleted.
Other journaling filesystems include XFS and ReiserFS.

An alternative to journaling is **copy-on-write**.
For modifying data, a snapshot is used.
Even when data modification is completed,
the old data is never overwritten.
COW filesystems include ZFS and Btrfs.

|name |                         description|
|-----|------------------------------------|
|fdisk|     manipulate disk partition table|
|mkfs |            build a Linux filesystem|
|fsch | check and repair a Linux filesystem|

Linux **Logical Volume Manager (LVM)** is used for managing logical volumes.

## 6. Installing software

Use **packege management system** to install software.

|Linux distribution | Package management system|
|-------------------|--------------------------|
|Debian             |                  dpkg/apt|
|Redhat             |                   rpm/yum|
|OpenSuse           |                    zypper|
|Archlinux          |                    pacman|
|Gentoo             |                    emerge|

## APPENDIX

Common Linux Directory Names

|directory | Usage|
|----------|-|
|/         | root of the virtual directory|
|/bin      | binary directory, where many GNU user-level utilities are stored|
|/boot     | boot directory|
|/dev      | device directory|
|/etc      | system configuration files directory|
|/home     | home directory|
|/lib      | library directory, where system and application library files are stored|
|/media    | media directory, a common place for mount points|
|/mnt      | mount directory, another common place for mount points|
|/opt      | optional directory, often used to store third-party software packages and data files|
|/proc     | process directory, where hardware and process information is stored|
|/root     | root home directory|
|/run      | run directory, where runtime data is held during system operation|
|/sbin     | binary directory, where many GNU admin-level utilities are stored|
|/srv      | service directory, where local services store their files|
|/sys      | system directory, where system hardware information files are stored|
|/tmp      | temporary directory|
|/usr      | user binary directory, where the bulk of GNU user-level utilities  and data files are stored|
|/var      | variable directory, for files that change frequently, such as log files|

Linux Process Signals

|Signal | Name | Description|
|-------|------|-|
|1      | HUP  | Hangs up|
|2      | INT  | Interrupts|
|3      | QUIT | Stops running|
|9      | KILL | Unconditionally terminates|
|11     | SEGV | Produces segment violation|
|15     | TERM | Terminates if possible|
|17     | STOP | Stops unconditionally, but doesn't terminate|
|18     | TSTP | Stops or pauses, but continues to run in background|
|19     | CONT | Resumes execution after STOP or TSTP|
