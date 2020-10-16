---
title: "A Tour of Computer Systems"
date: 2020-05-06T20:48:24+08:00
draft: true
toc: true
tags:
  - computer systems
---

## 1. Compilation

All information in a system, including disk files, programs stored in memory,
is represented as a branch bits.
The only thing that distinguishes different data objects is the context.

A c program starts from **source file**.
The source file has to be translated to an **executable file**.
The translation programs are known as **compilation system**,
including four phases:

* preprocessing
* compilation
* assembly
* linking

## 2. Execution

A hardware system includes four parts:

* buses
* I/O devices
* memory
* processor

When executing a program, the executable file is loaded from disk to memory.
The processor begins executing the instructions in the program.
Then the results will be sent to some I/O devices

Usually, the system spends a lot of time move program and data from one place
to another place.
To solve this problem, a hierarchy storage strategy is adopted.

* cpu register
* L1 cache
* L2 cache
* L3 cache
* memory
* local disk
* remote storage (distributed file systems, web servers)

## 3. Operating system

The operating system has two primary purposes:

* to protect the hardware from misuse by programs
* to provide programs with simple and uniform mechanisms for manipulating
  complicated and wildly different low-level hardware devices

The operating system achieves both goals via the fundamental abstractions:

* processes
  * threads
* virtual memory
* files

## 4. Network

Different systems communicate with each other using networks.

## 5. Concurrency and parallelism

We always want the computers do more and do fast,
that is what concurrency and parallelism do.
**Concurrency** refers to the general concept of a system with multiple,
simultaneous activities.
**Parallelism** refers to the use of concurrency to make a system run faster.

There are three levels of parallelism:

* thread-level concurrency
* instruction-level parallelism
* single-instruction, multiple-data parallelism
