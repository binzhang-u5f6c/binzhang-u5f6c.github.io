---
title: "Heapsort"
date: 2021-02-24T19:41:13+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

## 1. Heaps

The **heap** data structure is an array that can be viewed as
nearly complete binary tree.
The tree is completely filled on all levels except possibly the lowest,
which is filled from the left up to a point.
The parent index of the \\(i\\)th element is \\(\lceil i/2 \rceil -1\\),
the left child index is \\(2i+1\\),
and the right child index is \\(2i+2\\).

There are two kinds of heaps, **max-heap** and **min-heap**.
In a max-heap, for every node other than the root,
the value is not larger than that of its parent.
In a min-heap, for every node other than the root,
the value is not smaller than that of its parent.

## 2. Maintaining the heap property

The algorithm is implemented below.

```c
void max_heapify(int *a, int n, int i)
{
    int l = 2 * i + 1, r = 2 * i + 2;
    int temp, temp_i = i;
    if (l < n && a[l] > a[i])
        temp_i = l;
    if (r < n && a[r] > a[temp_i])
        temp_i = r;
    if (temp_i != i)
    {
        temp = a[i];
        a[i] = a[temp_i];
        a[temp_i] = temp;
        max_heapify(a, n, temp_i);
    }
    return;
}
```

## 3. Building a heap

The algorithm is implemented below.

```c
void build_max_heap(int *a, int n)
{
    int i = n / 2 - 1;
    for (; i >= 0; --i)
        max_heapify(a, n, i);
    return;
}
```

## 4. The heapsort algorithm

The algorithm is implemented below.

```c
void heap_sort(int *a, int n)
{
    int i, temp;
    build_max_heap(a, n);
    for (i = n - 1; i > 0; --i)
    {
        temp = a[0];
        a[0] = a[i];
        a[i] = temp;
        max_heapify(a, i, 0);
    }
    return;
}
```

The running time of heapsort is \\(O(n\log n)\\).
