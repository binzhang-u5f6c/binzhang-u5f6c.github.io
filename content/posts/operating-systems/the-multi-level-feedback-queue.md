---
title: "The Multi-Level Feedback Queue"
date: 2020-10-15T21:42:59+08:00
draft: true
toc: true
tags:
  - operating systems
---

**Multi-level Feedback Queue** (MLFQ) is the one of the  most popular scheduling
algorithm in the world.
Many systems, including BSD/UNIX derivatives, Solaris and Windows NT
use a form of MLFQ as their base scheduler.

## 1. MLFQ: Basic Idea

MLFQ has a number of distinct **queues**,
each assigned a different **priority level**.
A job with higher priority will be run firstly.
Jobs on a queue have the same priority,
and are scheduled by RR.

MLFQ varies the priority based on its observed behavior.
When a job enters the system,
it is placed at the highest priority.
If it uses up an entire time slice,
its priority is reduced.
If it gives up the CPU before the time slice is up,
it stays at the same priority level.

## 2. The Priority Boost and Better Accounting

However, the approach contains serious flaws.

* **Starvation**: if there are too many interactive jobs,
  there is no CPU time left for long-running jobs.
* A job can change its behavior.
  It can be more interactive after a period of time.
  Higher priority is needed.
* A job can **game** the scheduler.
  It gives up the CPU just before the time slice is over.
  Thus it stays at the highest priority level all the time.

To solve the first two problems, after some time period,
all jobs are moved to the topmost queue,
which is called **priority boost**.

To solve the third problem, the accounting of CPU time should be improved.
Once a job uses up its time allotment at a given level,
regardless of how many times it has given up the CPU,
its priority is reduced.

## 3. Tuning MLFQ And Other Issues

One big question is how to **parameterize** the scheduler.
Parameters, such as time slice, queues number, time period for priority boost,
are called **voo-doo constants**.
They seem to require some form of black magic to set them correctly.
Only some experience with workloads and subsequent tuning of the scheduler will
lead to a satisfactory balance.

Usually the high-priority queues have short time slides,
while low-priority queues have long time slides.

Some MLFQ implementations, like FreeBSD scheduler,
use mathematical formulae to adjust priorities.
Some implementations provide users ways to give advice for setting priorities,
such as `nice`.
