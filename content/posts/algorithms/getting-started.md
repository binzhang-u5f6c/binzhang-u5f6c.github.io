---
title: "Getting Started"
date: 2020-05-18T21:19:27+08:00
draft: false
toc: true
mathjax: true
tags:
  - algorithms
---

## 1. Insertion sort

Insertion sort is implemented below.

```python
def insertion_sort(a):
    for j in range(1, len(a)):
        key = a[j]
        i = j - 1
        while (i >= 0) and (a[i] > key):
            a[i+1] = a[i]
            i -= 1
        a[i+1] = key
    return a
```

We use **loop invariants** to proof the correctness of our algorithm.
A loop invariant include three parts:

* It's true prior to the first iteration.
* If it's true before an iteration, it remain true before the next iteration.
* When the loop terminates, the invariant give us a useful property.

## 2. Analyzing algorithms

The worst-case running time of insertion sort is \\(\Theta(n^2)\\).

## 3. Designing algorithms

We use the idea of **divide-and-conquer** to design a sort algorithm,
merge sort.
It is implemented below.

```python
def merge(a, b, sentinel=True):
    m, n = len(a), len(b)
    c = list(range(m+n))
    i, j = 0, 0
    a.append(0xffffffff)
    b.append(0xffffffff)
    for k in range(m+n):
        if a[i] <= b[j]:
            c[k] = a[i]
            i += 1
        else:
            c[k] = b[j]
            j += 1
    return c


def merge_sort(a):
    n = len(a)
    if n >= 2:
        m = n // 2
        b = merge_sort(a[:m])
        c = merge_sort(a[m:])
        a = merge(b, c, False)
    return a
```

The worst-case running time of merge sort is \\(\Theta(n\log n)\\).
