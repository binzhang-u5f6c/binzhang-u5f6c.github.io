---
title: "Processes"
date: 2020-10-15T21:17:01+08:00
draft: true
toc: false
tags:
  - operating systems
---

The process refers to a **running program**.

The OS virtualizes CPU via **time sharing** mechanism.
(Here we call the low-level machinery **mechanism**,
and the top-level machinery **policy**.)

Process API:

* create
* destroy
* wait
* miscellaneous control
* status

To create a process:

* load the code and static data from disk to memory
* allocate some memory for run-time stack
* allocate some memory for program's heap
* do some IO-related initialization tasks
* transfer the control of CPU to the process

The process states:

* running
* ready
* blocked

The OS keep a **process list** data structure for all processes.
