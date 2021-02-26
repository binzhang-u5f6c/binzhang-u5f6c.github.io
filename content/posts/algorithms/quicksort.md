---
title: "Quicksort"
date: 2021-02-26T20:17:11+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

## 1. Description of quicksort

The quicksort algorithm is implemented below.

```c
int partition(int *a, int b, int e)
{
    int ae = a[e];
    int i = b - 1, j = b;
    for (; j < e; ++j)
    {
        if (a[j] <= ae)
        {
            ++i;
            int temp = a[i];
            a[i] = a[j];
            a[j] = temp;
        }
    }
    a[e] = a[i+1];
    a[i+1] = ae;
    return i + 1;
}

void quicksort_r(int *a, int b, int e)
{
    if (b < e)
    {
        int m = partition(a, b, e);
        quicksort_r(a, b, m-1);
        quicksort_r(a, m+1, e);
    }
    return;
}

void quicksort(int *a, int n)
{
    quicksort_r(a, 0, n-1);
    return;
}
```

## 2. Performance of quicksort

The worst-case behavior occurs when the partitioning routine produces
one subproblem with \\(n-1\\) elements and one with 0 elements.
And the running time is \\(\Theta(n^2)\\).

The expected running time is \\(\Theta(n\log n)\\).

## 3. A randomized version of quicksort

The randomized_quicksort algorithm is implemented below.

```c
#include <stdlib.h>

int randomized_partition(int *a, int b, int e)
{
    int i = rand() % (e - b + 1) + b;
    int temp = a[i];
    a[i] = a[e];
    a[e] = temp;
    return partition(a, b, e);
}

void randomized_quicksort_r(int *a, int b, int e)
{
    if (b < e)
    {
        int m = partition(a, b, e);
        randomized_quicksort_r(a, b, m-1);
        randomized_quicksort_r(a, m+1, e);
    }
    return;
}

void randomized_quicksort(int *a, int n)
{
    randomized_quicksort_r(a, 0, n-1);
    return;
}
```
