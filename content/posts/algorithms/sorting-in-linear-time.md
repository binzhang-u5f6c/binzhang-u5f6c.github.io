---
title: "Sorting in Linear Time"
date: 2021-02-26T21:11:15+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

## 1. Lower bounds for sorting

Any comparision sort algorithm requires \\(\Omega(n \log n)\\) comparisions
int the worst case.

## 2. Counting sort

The counting sort algorithm is implemented below.

```c
#include <stdlib.h>

void counting_sort(int *a, int n, int k)
{
    int *b = (int *) malloc(sizeof(int)*n);
    int *c = (int *) malloc(sizeof(int)*k);
    int i;
    for (i = 0; i < k; ++i)
        c[i] = 0;
    for (i = 0; i < n; ++i)
        ++c[a[i]];
    for (i = 1; i < k; ++i)
        c[i] += c[i-1];
    for (i = n-1; i >= 0; --i)
    {
        b[c[a[i]]-1] = a[i];
        --c[a[i]];
    }
    for (i = 0; i < n; ++i)
        a[i] = b[i];
    free(b); free(c);
    return;
}
```

## 3. Radix sort

The radix sort algorithm is implemented below.

```c
#include <stdlib.h>

void radix_sort(int *a, int n, int d)
{
    int *b = (int *) malloc(sizeof(int)*n);
    int *c = (int *) malloc(sizeof(int)*10);
    int i, j, e = 1;
    for (i = 0; i < d; ++i)
    {
        for (j = 0; j < 10; ++j)
            c[j] = 0;
        for (j = 0; j < n; ++j)
            ++c[a[j]/e%10];
        for (j = 1; j < 10; ++j)
            c[j] += c[j-1];
        for (j = n-1; j >= 0; --j)
        {
            b[c[a[j]/e%10]-1] = a[j];
            --c[a[j]/e%10];
        }
        for (j = 0; j < n; ++j)
            a[j] = b[j];
        e *= 10;
    }
    free(b); free(c);
    return;
}
```
