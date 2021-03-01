---
title: "Medians and Order Statistics"
date: 2021-03-01T20:02:04+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

The **\\(i\\)th order statistic** of a set of \\(n\\) elements
is the \\(i\\)th smallest element.

A **selection problem** is to select the \\(i\\)th order statistic
from a set of \\(n\\) distinct numbers.

## 1. Minimum and maximum

Every element except the winner must lose once in a comparison.
Hence \\(n-1\\) comparisions are necessary to determine the minimum.
The algorithm is implemented below.

```c
int minimum(int *a, int n)
{
    int i, min = a[0];
    for (i = 1; i < n; ++i)
        if (min > a[i])
            min = a[i];
    return min;
}
```

If we want to find the minimum and maximum simultaneously,
only \\(3\lfloor n/2 \rfloor\\) comparisions are necessary.
The algorithm is implemented below.

```c
void min_and_max(int *a, int n, int *mm)
{
    int i = (n % 2 == 1)?1:0;
    mm[0] = a[0]; mm[1] = a[0];
    for (; i < n-1; i += 2)
        if (a[i] < a[i+1])
        {
            if (a[i] < mm[0])
                mm[0] = a[i];
            if (a[i+1] > mm[1])
                mm[1] = a[i+1];
        }
        else
        {
            if (a[i+1] < mm[0])
                mm[0] = a[i+1];
            if (a[i] > mm[1])
                mm[1] = a[i];
        }
    return;
}
```

## 2. Selection in expected linear time

The randomized select algorithm is implemented below.

```c
#include <stdlib.h>

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

int randomized_partition(int *a, int b, int e)
{
    int i = rand() % (e - b + 1) + b;
    int temp = a[i];
    a[i] = a[e];
    a[e] = temp;
    return partition(a, b, e);
}

int randomized_select_r(int *a, int b, int e, int i)
{
    if (b == e)
        return a[b];
    int m = randomized_partition(a, b, e);
    if (i == m - b)
        return a[b];
    else if (i < m - b)
        return randomized_select_r(a, b, m-1, i);
    else
        return randomized_select_r(a, m+1, e, i-(m-b)-1);
}

int randomized_select(int *a, int n, int i)
{
    return randomized_select_r(a, 0, n-1, i);
}
```

The worst-case running time is \\(\Theta(n^2)\\).
If the elements are distinct, the expected running time is \\(O(n)\\).
