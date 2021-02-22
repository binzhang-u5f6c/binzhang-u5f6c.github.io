---
title: "Probabilistic Analysis and Randomized Algorithms"
date: 2021-02-22T19:34:56+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

## 1. The hiring problem

We have used **worst-case analysis** in the previous analysis of problems.
Another approach is **probabilistic analysis**,
in which we analyze algorithms via **average-case running time**.

## 2. Indicator random variables

The **indicator random variable** associated with event \\(A\\) is defined as
\[I(A) = \begin{cases}
1 & \text{if \\(A\\) occurs,} \\\\
0 & \text{if \\(A\\) does not occur.} \end{cases}\]

Using indicator random variables,
we obtain the average-case hiring cost of the hiring problem is
\\(O(c \log n)\\).

## 3. Randomized algorithms

In order to use probabilistic analysis,
we need to know the distribution of the inputs.
In many cases, we know very little about it.
Yet we often use probability and randomness as a tool
for algorithm design and analysis.
An algorithm is **randomized**
if its behavior is determined not only by its input
but also by values produced by a random-number generator.

The permuting arrays algorithms are implemented below.

```c
#include <stdlib.h>

void permute_by_sorting(int *a, int n)
{
    int *p = malloc(sizeof(int)*n);
    int i, j, temp_p, temp_a;
    for (i = 0; i < n; ++i)
        p[i] = rand();
    for (i = 1; i < n; ++i)
    {
        temp_p = p[i];
        temp_a = a[i];
        j = i - 1;
        while (j >= 0 && p[j] > temp_p)
        {
            p[j+1] = p[j];
            a[j+1] = a[j];
            j -= 1;
        }
        p[j+1] = temp_p;
        a[j+1] = temp_a;
    }
    return;
}

void randomize_in_place(int *a, int n)
{
    int i, j, temp;
    for (i = 0; i < n; ++i)
    {
        j = rand() % n;
        temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }
    return;
}
```
