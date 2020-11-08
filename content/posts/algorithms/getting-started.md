---
title: "Getting Started"
date: 2020-05-18T21:19:27+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

## 1. Insertion sort

Insertion sort is implemented below.

```c
void insertion_sort(int *a, int n)
{
    int i, j, temp;
    for (i = 1; i < n; ++i)
    {
        temp = a[i];
        j = i - 1;
        while (j >= 0 && a[j] > temp)
        {
            a[j+1] = a[j];
            j -= 1;
        }
        a[j+1] = temp;
    }
    return;
}
```

We use **loop invariants** to proof the correctness of our algorithm.
A loop invariant includes three parts:

* It's true prior to the first iteration.
* If it's true before an iteration, it remain true before the next iteration.
* When the loop terminates, the invariant give us a useful property.

## 2. Analyzing algorithms

The worst-case running time of insertion sort is \\(\Theta(n^2)\\).

## 3. Designing algorithms

We use the idea of **divide-and-conquer** to design a sort algorithm,
merge sort.
It is implemented below.

```c
#include <stdlib.h>

void merge(int *a, int b, int m, int e)
{
    int i, j, k;
    int *a1 = malloc(sizeof(int)*(m-b+2));
    for (i = b; i <= m; ++i)
        a1[i-b] = a[i];
    a1[m-b+1] = 0x7FFFFFFF;
    int *a2 = malloc(sizeof(int)*(e-m+1));
    for (j = m+1; j <= e; ++j)
        a2[j-m-1] = a[j];
    a2[e-m] = 0x7FFFFFFF;

    i = 0; j = 0;
    for (k = b; k <= e; ++k)
    {
        if (a1[i] <= a2[j])
            {a[k] = a1[i]; ++i;}
        else
            {a[k] = a2[j]; ++j;}
    }
    return;
}

void merge_sort(int *a, int b, int e)
{
    if (e > b)
    {
        int m = (e + b) / 2;
        merge_sort(a, b, m);
        merge_sort(a, m+1, e);
        merge(a, b, m, e);
    }
    return;
}
```

The worst-case running time of merge sort is \\(\Theta(n\log n)\\).
