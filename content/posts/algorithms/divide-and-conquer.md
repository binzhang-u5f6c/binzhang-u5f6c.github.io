---
title: "Divide-and-Conquer"
date: 2020-08-17T21:45:26+08:00
draft: true
toc: true
mathjax: true
tags:
  - algorithms
---

## 1. The maximum-subarray problem

The algorithm is implemented below.

```python
def find_maximum_subarray(a, low=None, high=None):
    if low == high:
        return low, high, a[low]
    mid = (low + high) // 2
    low1, high1, max1 = find_maximum_subarray(a, low, mid)
    low2, high2, max2 = find_maximum_subarray(a, mid+1, high)

    # left part
    sum_left = 0
    max3_left = a[mid]
    for i in range(mid, low-1, -1):
        sum_left += a[i]
        if sum_left >= max3_left:
            low3 = i
            max3_left = sum_left
    # right part
    sum_right = 0
    max3_right = a[mid+1]
    for i in range(mid+1, high+1):
        sum_right += a[i]
        if sum_right >= max3_right:
            high3 = i
            max3_right = sum_right
    max3 = max3_left + max3_right

    if max1 >= max2 and max1 >= max3:
        return low1, high1, max1
    elif max2 >= max3:
        return low2, high2, max2
    else:
        return low3, high3, max3
```

## 2. Strassen's algorithm for matrix multiplication

The square matrix multiply algorithm is implemented below.

```python
def square_matrix_multiply(a, b):
    n = len(a)
    c = [[0 for i in range(n)] for j in range(n)]
    for i in range(n):
        for j in range(n):
            for k in range(n):
                c[i][j] += a[i][k] * b[k][j]
    return c
```

The simple divide-and-conquer version is implemented below.

```python
def square_matrix_multiply_recursive(a, index_a, b, index_b):
    n = index_a[2] - index_a[0] + 1
    c = [[0 for i in range(n)] for j in range(n)]
    if n == 1:
        c[0][0] = a[index_a[0]][index_a[1]] * b[index_b[0]][index_b[1]]
    else:
        m = n // 2
        index_a11 = (index_a[0], index_a[1], index_a[0]+m-1, index_a[1]+m-1)
        index_a12 = (index_a[0], index_a[1]+m, index_a[0]+m-1, index_a[3])
        index_a21 = (index_a[0]+m, index_a[1], index_a[2], index_a[1]+m-1)
        index_a22 = (index_a[0]+m, index_a[1]+m, index_a[2], index_a[3])
        index_b11 = (index_b[0], index_b[1], index_b[0]+m-1, index_b[1]+m-1)
        index_b12 = (index_b[0], index_b[1]+m, index_b[0]+m-1, index_b[3])
        index_b21 = (index_b[0]+m, index_b[1], index_b[2], index_b[1]+m-1)
        index_b22 = (index_b[0]+m, index_b[1]+m, index_b[2], index_b[3])
        c111 = square_matrix_multiply_recursive(a, index_a11, b, index_b11)
        c112 = square_matrix_multiply_recursive(a, index_a12, b, index_b21)
        c121 = square_matrix_multiply_recursive(a, index_a11, b, index_b12)
        c122 = square_matrix_multiply_recursive(a, index_a12, b, index_b22)
        c211 = square_matrix_multiply_recursive(a, index_a21, b, index_b11)
        c212 = square_matrix_multiply_recursive(a, index_a22, b, index_b21)
        c221 = square_matrix_multiply_recursive(a, index_a21, b, index_b12)
        c222 = square_matrix_multiply_recursive(a, index_a22, b, index_b22)
        for i in range(m):
            for j in range(m):
                c[i][j] = c111[i][j] + c112[i][j]
                c[i][j+m] = c121[i][j] + c122[i][j]
                c[i+m][j] = c211[i][j] + c212[i][j]
                c[i+m][j+m] = c221[i][j] + c222[i][j]
    return c
```

The Strassen's method is implemented below.

```python
def square_matrix_multiply_strassens_method(a, index_a, b, index_b):
    n = index_a[2] - index_a[0] + 1
    c = [[0 for i in range(n)] for j in range(n)]
    if n == 1:
        c[0][0] = a[index_a[0]][index_a[1]] * b[index_b[0]][index_b[1]]
    else:
        m = n // 2
        index_a11 = (index_a[0], index_a[1], index_a[0]+m-1, index_a[1]+m-1)
        index_a12 = (index_a[0], index_a[1]+m, index_a[0]+m-1, index_a[3])
        index_a21 = (index_a[0]+m, index_a[1], index_a[2], index_a[1]+m-1)
        index_a22 = (index_a[0]+m, index_a[1]+m, index_a[2], index_a[3])
        index_b11 = (index_b[0], index_b[1], index_b[0]+m-1, index_b[1]+m-1)
        index_b12 = (index_b[0], index_b[1]+m, index_b[0]+m-1, index_b[3])
        index_b21 = (index_b[0]+m, index_b[1], index_b[2], index_b[1]+m-1)
        index_b22 = (index_b[0]+m, index_b[1]+m, index_b[2], index_b[3])

        s1 = [[
               b[index_b12[0]+i][index_b12[1]+j] - b[index_b22[0]+i][index_b22[1]+j]
               for i in range(m)]
              for j in range(m)]
        s2 = [[
               a[index_a11[0]+i][index_a11[1]+j] + a[index_a12[0]+i][index_a12[1]+j]
               for i in range(m)]
              for j in range(m)]
        s3 = [[
               a[index_a21[0]+i][index_a21[1]+j] + a[index_a22[0]+i][index_a22[1]+j]
               for i in range(m)]
              for j in range(m)]
        s4 = [[
               b[index_b21[0]+i][index_b21[1]+j] - b[index_b11[0]+i][index_b11[1]+j]
               for i in range(m)]
              for j in range(m)]
        s5 = [[
               a[index_a11[0]+i][index_a11[1]+j] + a[index_a22[0]+i][index_a22[1]+j]
               for i in range(m)]
              for j in range(m)]
        s6 = [[
               b[index_b11[0]+i][index_b11[1]+j] + b[index_b22[0]+i][index_b22[1]+j]
               for i in range(m)]
              for j in range(m)]
        s7 = [[
               a[index_a12[0]+i][index_a12[1]+j] - a[index_a22[0]+i][index_a22[1]+j]
               for i in range(m)]
              for j in range(m)]
        s8 = [[
               b[index_b21[0]+i][index_b21[1]+j] + b[index_b22[0]+i][index_b22[1]+j]
               for i in range(m)]
              for j in range(m)]
        s9 = [[
               a[index_a11[0]+i][index_a11[1]+j] - a[index_a21[0]+i][index_a21[1]+j]
               for i in range(m)]
              for j in range(m)]
        s10 = [[
                b[index_b11[0]+i][index_b11[1]+j] + b[index_b12[0]+i][index_b12[1]+j]
                for i in range(m)]
               for j in range(m)]

        index_s = (0, 0, m-1, m-1)
        p1 = square_matrix_multiply_strassens_method(a, index_a11, s1, index_s)
        p2 = square_matrix_multiply_strassens_method(s2, index_s, b, index_b22)
        p3 = square_matrix_multiply_strassens_method(s3, index_s, b, index_b11)
        p4 = square_matrix_multiply_strassens_method(a, index_a22, s4, index_s)
        p5 = square_matrix_multiply_strassens_method(s5, index_s, s6, index_s)
        p6 = square_matrix_multiply_strassens_method(s7, index_s, s8, index_s)
        p7 = square_matrix_multiply_strassens_method(s9, index_s, s10, index_s)

        for i in range(m):
            for j in range(m):
                c[i][j] = p5[i][j] + p4[i][j] - p2[i][j] + p6[i][j]
                c[i][j+m] = p1[i][j] + p2[i][j]
                c[i+m][j] = p3[i][j] + p4[i][j]
                c[i+m][j+m] = p5[i][j] + p1[i][j] - p3[i][j] -p7[i][j]
    return c
```

## 3. The substitution method for solving recurrences

The **substitution method** comprises two steps:

* Guess the form of the solution.
* Use mathematical induction to find the constants and show that the solution
  works.

**Subtle skill**: Sometimes you might correctly guess the solution of a recurrence,
but some how the math fails to work out.
For example, consider the recurrence,

\\[T(n) = T(\lfloor n/2 \rfloor) + T(\lceil n/2 \rceil) + 1. \\]

We guess the solution is \\(T(n) = O(n)\\).
We obtain,

\\[T(n) \leq c\lfloor n/2 \rfloor + c\lceil n/2 \rfloor + 1 = cn + 1,\\]

which does not implies \\(T(n) \leq cn\\).
If our guess is \\(T(n) \leq cn - d\\), we have

\\[T(n) \leq cn -2d + 1.\\]

which implies that \\(T(n) \leq cn -d\\) as long as \\(d \geq 1\\).
