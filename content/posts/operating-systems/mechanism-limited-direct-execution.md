---
title: "Mechanism: Limited Direct Execution"
date: 2020-10-15T21:28:26+08:00
draft: true
toc: true
tags:
  - operating systems
---

The basic technique is **direct execution**.
It's simple but it gives rise to a few problems.

## 1. Problem #1: Restricted Operations

CPU provide two execution mode:

* kernel mode
* user mode

along with two special instructions:

* trap
* return-from-trap

Code that runs in user mode is restricted in what it can do.
When doing some **privileged** operations,
it executes a trap instruction and performs a **system call**.
The trap instruction raises the privilege level to kernel mode.
The OS handles the system call,
and calls a return-from-trap instruction when finishing.
The privilege level returns to user mode and origin code continues to run.

How does the CPU knows which code should be executed after a trap instruction?
The OS sets up a **trap table** at boot time,
informing the hardware of the locations of **trap handlers**.
The trap table cannot be modified in user mode.
Every system call have a **system-call number**.
The user code only needs to provide the number when performing a system call.
The hardware will do the following.

## 2. Problem #2: Switching Between Processes

To switch between processes, the OS has to regain control of the CPU firstly.
A **timer interrupt** is used to raise interrupt every so many milliseconds.
As we discussed in Problem #1, the OS sets up a trap table at boot time.
The OS also informs the hardware the location of the **interrupt handler**,
and starts the timer at boot time.

The OS regains the control of CPU, and decides whether to switch.
If the decision is to switch,
the OS executes a low-level piece of code called **context switch**.
The context switch code saves the general purpose registers, PC, and the kernel
stack pointer of the currently-running process,
and then restores said registers, PC, and switch to the kernel stack for the
soon-to-be-executing process.

No matter whether context switch is executed,
the OS executes a return-from-trap instruction,
and the soon-to-be-executing process becomes the currently-running process.

**Note**: there are two types of register saves/restores.

* The first is when the timer interrupt occurs.
  The saves/restores are handled by hardware,
  using the kernel stack of the process.
* The second is when the OS decide to switch.
  The saves/restores are handled by software,
  using memory.

The first case saves/restores are the same after trap instructions
and before return-from-trap instructions in Problem #1.
