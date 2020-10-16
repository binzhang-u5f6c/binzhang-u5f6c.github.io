---
title: "Scheduling: Introduction"
date: 2020-10-15T21:40:17+08:00
draft: true
toc: true
tags:
  - operating systems
---

We have discussed about the low-level **mechanisms** of running processes.
In this chapter, we will talk about the high-level **policies**
that an OS scheduler employs.

## 1. Workload Assumptions

To simplify the problems, we make five assumptions:

* Each job runs for the same amount of time
* All jobs arrive at the same time.
* Once started, each job runs to completion.
* All jobs only use the CPU.
* The run-time of each job is known.

Here **jobs** refer to the processes running in the system.

## 2. Scheduling Metrics

The first metric we used is **turnaround time**.
The turnaround time of a job is defined as the time
at which the job completes minus the time
at which the job arrived in the system.
Turnaround time is a performance metric,
which will be our primary focus this chapter.

Now days, users would demand interactive performance from the system.
A new metric was born: **response time**.
The response time is defined as the time from when the job arrives in a system
to the first time it is scheduled.

The last metric of interest is **fairness**,
as measured by **Jain's Fairness Index**.

## 3. First In, First Out (FIFO)

The most basic algorithm is known as **First In, First Out (FIFO)** scheduling.

## 4. Shortest Job First (SJF)

If we relax assumption 1,
FIFO suffers from the problem called **convoy effect**,
where a number of relatively-short potential consumers of a resource get queued
behind a heavyweight resource consumer.
To solve the problem,
**Shortest Job First (SJF)** scheduling discipline is proposed.
It runs the shortest job first,
then the next shortest,
and so on.

In fact, SJF is an **optimal** scheduling algorithm under our five assumptions.

## 5. Shortest Time-to-Completion First (STCF)

Now we relax assumption 2.
Jobs can arrive at any time.
SJF is a **non-preemptive** scheduling algorithm.
Short jobs arriving later can get queued behind a long, current-running job.
To solve the problem, we have to relax assumption 3.
**Shortest Time-to-Completion First (STCF)** adds preemption to SJF.
Any time a new job enters the system,
the STCF scheduler determines which of the remaining jobs has the least time
left, and schedules that one.

## 6. Round Robin

STCF is good for turnaround time, but not for response time.
**Round Robin (RR)** is sensitive to response time.
RR runs a job for a **time slice** (sometimes called a **scheduling quantum**),
and then switches to the next job in the run queue.

The length of the time slice is critical for RR.
The shorter it is,
the better the performance of RR under the response-time metric.
But short time slice leads to high cost of context switching.
System designer has to make it long enough to **amortize** the cost of
context switching.

However, RR is one of the worst policies if turnaround time is our metric.
More generally, any policy that is fair,
will perform poorly on metrics such as turnaround time.
It is an inherent trade-off.

## 7. Incorporating IO

At last, we relax assumption 4.
The scheduler treats each CPU burst as a job,
such that processes that are interactive get run frequently.
While those interactive jobs are performing IO,
other CPU-intensive jobs run,
thus better utilizing the processor.
