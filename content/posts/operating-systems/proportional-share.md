---
title: "Proportional Share"
date: 2020-10-15T21:48:33+08:00
draft: true
toc: true
tags:
  - operating systems
---

**Proportional-share** schedulers are another type of schedulers,
also sometimes referred to as **fair-share** schedulers.

## 1. Lottery Scheduling

An excellent early example of proportional-share scheduling is **lottery
scheduling**.
Underlying lottery scheduling is one very basic concept:
**tickets** represent your share.
The percent of tickets that a process has represents its share of the system
resource.

There are three ticket mechanisms to manipulate tickets:

* **Ticket currency** allows users with a set of tickets to allocate tickets
  among their own jobs in whatever currency they would like.
* **Ticket transfer** allows a process temporarily hand off its tickets to
  another process.
* **Ticket inflation** allows a process temporarily raise or lower the number
  of tickets it owns.

To implement a lottery scheduling,
all you need is a good random number generator to pick the winning ticket,
a data structure to track the processes of the system (e.g. a list),
and the total number of tickets.

To make it more efficient,
it might be best to organize the list in descending order,
especially if there are a few processes that possess most of the tickets.

## 2. Stride Scheduling

Instead of using randomness,
**stride scheduling** is a deterministic fair-share scheduler.
Every job in the system has a **stride** value and a **pass** value.
The stride value is inverse in proportion to the number of tickets in lottery
scheduling.
Pass value of each job is set at 0 initially.
The scheduler chooses the job with lowest pass value to run.
When running a job,
the pass value is incremented by its stride value.

Compared with the deterministic scheduling,
lottery scheduling is simple,
and makes it much easier to incorporate new processes.

## 3. The Linux Completely Fair Scheduler (CFS)

The current Linux scheduler, **Completely Fair Scheduler (CFS)**,
implements fair-share scheduling in a highly efficient and scalable manner.

As each process runs, it accumulates **virtual runtime**.
The virtual time of each process increases at the same rate by default,
which is in proportion with physical real time.
The scheduler pick the process with the lowest virtual time to run.

CFS uses `sched_latency` to determine time slice for a process.
If there are \\(n\\) processes,
the per-process time slice is \\(\text{sched_latency} / n\\).
CFS uses `min_granularity` to determine the lower bound of the per-process time
slice,
such that not too much time is spent in scheduling overhead.

CFS provides a way for users to give some processes a higher share of the CPU,
known as the **nice** level.
The nice level can be set from -20 to 19, 0 by default.
CFS maps the nice level to a weight,
which is used to determine the time slice.
And higher weight also implies fewer virtual time.

CFS keep processes in a red-black tree rather than lists,
making most operations more efficient.
CFS does not keep all processes;
rather only running processes are kept therein.

For jobs that wake up from sleeping,
CFS set the virtual time to the minimum value found in the tree.
