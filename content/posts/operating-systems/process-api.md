---
title: "Process API"
date: 2020-10-15T21:22:33+08:00
draft: true
toc: true
tags:
  - operating systems
---

## 1. Mostly used API

`fork()` creates a new process by duplicating the calling process.
Return 0 if child process, and child process id if parent process.

`wait()` wait for state changes in a child of the calling process.
Return the process id of the terminated child process.

There are 6 programs in `exec()` family: `execl()`, `execlp()`, `execle()`,
`execv()`, `execvp()`, `execvpe()`.

* `l` means that arguments are passed via multiple strings
* `v` means that arguments are passed via an array of strings
* `p` means to search for an executable file if the specified filename does not
  contain a slash (/) character
* `e` means that caller can specify the environment of the executed program

## 2. Shell

An example of usage of the combination of `fork()`, `wait()` and `exec()`
is shell.

## 3. Other API

To control the processes, `kill()` is used to send **signal** to a process,
and `signal()` is used to catch various signals.
