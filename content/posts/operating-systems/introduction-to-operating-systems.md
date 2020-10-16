---
title: "Introduction to Operating Systems"
date: 2020-05-08T20:51:22+08:00
draft: true
toc: true
tags:
  - operating systems
---

The **Operating System** is known as a **resource manager**.
It takes resources, such as CPU and memory, and **virtualizes** them.
It provides a **standard library** to applications.

## 1. Virtualizing the CPU

The operating system is in charge of a illusion that the system has a very
large number of virtual CPUs and each program runs on its own CPU.

## 2. Virtualizing Memory

The operating system virtualizes memory such that each process accesses its
own private **virtual address space**,
which the OS maps onto the physical memory of the machine.

## 3. Concurrency

Concurrency refer to a host of problems that arise when working multiple task
at once in the same program.

**Note**: Thus the OS considers about thread-level concurrency.

## 4. Persistence

Persistence refer to storing data persistently.
