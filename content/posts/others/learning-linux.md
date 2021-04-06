---
title: "Learning Linux"
date: 2020-06-11T21:25:13+08:00
draft: false
toc: true
math: false
tags:
  - others
---

## 1. Basic commands

Navigate the filesystem & manipulate directories and files.

|name  |        description|
|------|-------------------|
|cd    |   change directory|
|ls    |               list|
|cp    |               copy|
|mv    |               move|
|rm    |             remove|
|ln    |       create links|
|mkdir | create directories|

View the contents of files.

|name|                    description|
|----|-------------------------------|
|file|            determine file type|
|cat | view entire contents of a file|
|less| view entire contents of a file|
|head|       view top lines of a file|
|tail|    view bottom lines of a file|

Monitor system status.

|name    |                         description|
|--------|------------------------------------|
|ps      |               show processes status|
|top     |   show processes status dynamically|
|free    |                   show memory usage|
|df      | report file system disk space usage|
|du      |           estimate file space usage|

Use commands.

|name   |                      description|
|-------|---------------------------------|
|man    |           show reference manuals|
|which  |    show the full path of command|
|type   | show description of command type|
|history|             show command history|
|alias  |        define or display aliases|

## 2. Run commands in shell

### 2.1 IO redirection

Redirect output.

```bash
# overwriting the file
command > file
# appending output to the file
command >> file
# suppressing output.
command > /dev/null
```

Redirect input.

```bash
command < inputfile
# inline input redirection
command << marker
data
marker
```

There is no dedicated redirection operator for redirecting errors.
We have to refer to its **file descriptor**.
The bash shell reserves the first three file descriptors as
STDIN (0), STDOUT (1) and STDERR (2).

```bash
command 2> file
```

Redirect both output and errors.

```bash
command 1> file1 2> file2
command &> file
```

### 2.2 Pipes

```bash
command1 | command2
```

Use `tee` to construct a T pipe.

```bash
# the output of command1 will be saved to file and passed to command2
command1 | tee file | command2
```

Some commands can be a filter in pipes.

|name |                     description|
|-----|--------------------------------|
|sort |                      sort lines|
|uniq |               remove duplicates|
|tr   |        transliterate characters|
|grep |       pattern matching by lines|
|sed  |                   stream editor|
|awk  | pattern scanning and processing|
|wc   |                      word count|

### 2.3 Expansion

Wildcards.

|Wildcard     | Meaning                            |
|-------------|------------------------------------|
|\*           | match any characters               |
|?            | match any single character         |
|[characters] | match any character in brackets    |
|[!characters]| match any character not in brackets|
|[[:class:]]  | match any character in class       |

Class include `[[:alnum:]]`, `[[:alpha:]]`, `[[:digit:]]`, `[[:lower:]]`
and `[[:upper:]]`.

Pathname expansion.

```bash
# list all txt files
ls *.txt
# tilde for home directory
cd ~
```

Arithmetic expansion.
It only supports integer arithmetic.

```bash
$((expression))
# the result is 2
echo $((5/2))
```

Brace expansion.

```bash
# the result is "file1 file2 file3"
echo file{1,2,3}
# use range
echo file{1..3}
# nested brace expansion
# the result is "file13 file14 file23 file 24"
echo file{1{3,4},2{3,4}}
```

Variable expansion.

```bash
$var
```

Command substitution.

```bash
$(command)
`command`
```

Double quoting.

```bash
# all characters are treated as ordinary characters
# except dollar sign, backslash and backquote
echo "~ will not be expanded as home directory"
echo "$var still show the value of var"
echo "backslash is used to escape special characters such as \$"
```

Single quoting.

```bash
# suppress all expansion
echo '$(ls)'
```

### 2.4 Jobs

Run commands one after one.

```bash
command1; command2
```

Commands are run in foreground mode by default.
Run commands in background mode.

```bash
# run in background
command &
# show jobs launched from current terminal
jobs
# return the job to foreground
fg %n
```

If you exit the terminal session,
all jobs, even in background mode, will be interrupted.
To avoid this, run scripts without a hang-up.

```bash
nohup command &
```

The `nohup` command disassociates the process from the terminal,
thus it redirects STDOUT and STDERR to `nohup.out` file.
Be careful if you run multiple commands using `nohup`.
All output is appended to `nohup.out`.

Use Ctrl+C to interrupt the current-running job.
Use Ctrl+Z to stop the current running job.
Usually we use Ctrl+Z to move the current-running job to background.

```bash
command
# stop by Ctrl+Z
# show the job number
jobs
# move to background
bg %n
# or return to foreground
fg %n
```

Use `kill` and `killall` to send other signals to processes.

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
|mount|               mount storage devices|
|fdisk|     manipulate disk partition table|
|mkfs |            build a Linux filesystem|
|fsck | check and repair a Linux filesystem|

Linux **Logical Volume Manager (LVM)** is used for managing logical volumes.

## 6. Utilities in Linux world

### 6.1 Package management system

Use **packege management system** to install software.

|Linux distribution | Package management system|
|-------------------|--------------------------|
|Debian             |                  dpkg/apt|
|Redhat             |                   rpm/yum|
|OpenSuse           |                    zypper|
|Archlinux          |                    pacman|
|Gentoo             |                    emerge|

### 6.2 Network

|name |                   description|
|-----|------------------------------|
|ping | verify the network connection|
|wget |              network download|
|curl |                transfer a URL|
|ssh  |                  remote login|
|scp  |              secure file copy|
|sftp |          secure file transfer|

### 6.3 Archive

|name |                               description|
|-----|------------------------------------------|
|tar  |                         archiving utility|
|gzip |                            compress files|
|rsync| remote file and directory synchronization|

### 6.4 Find files

|name  |            description|
|------|-----------------------|
|touch |  change file timestamp|
|find  |             find files|
|xargs | transfer input to args|

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
