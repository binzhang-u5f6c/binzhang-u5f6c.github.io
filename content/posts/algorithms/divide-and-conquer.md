---
title: "Divide-and-Conquer"
date: 2020-08-17T21:45:26+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

## 1. The maximum-subarray problem

The algorithm is implemented below.

```c
void find_maximum_subarray(int *a, int *low, int *high)
{
    if (*low == *high)
        return;
    int mid = (*low + *high) / 2, i;

    // left recursion
    int low1 = *low, high1 = mid;
    find_maximum_subarray(a, &low1, &high1);
    int max1 = 0;
    for (i = low1; i <= high1; ++i)
        max1 += a[i];

    // right recursion
    int low2 = mid+1, high2 = *high;
    find_maximum_subarray(a, &low2, &high2);
    int max2 = 0;
    for (i = low2; i <= high2; ++i)
        max2 += a[i];

    // subarray across mid point
    // left part
    int low3, sum_left = 0, max3_left = a[mid];
    for (i = mid; i >= *low; --i)
    {
        sum_left += a[i];
        if (sum_left >= max3_left)
            {low3 = i; max3_left = sum_left;}
    }
    // right part
    int high3, sum_right = 0, max3_right = a[mid+1];
    for (i = mid+1; i <= *high; ++i) {
        sum_right += a[i];
        if (sum_right >= max3_right)
            {high3 = i; max3_right = sum_right;}
    }
    // merge
    int max3 = max3_left + max3_right;

    // return
    if (max1 >= max2 && max1 >= max3)
        {*low = low1; *high = high1;}
    else if (max2 >= max3)
        {*low = low2; *high = high2;}
    else
        {*low = low3; *high = high3;}
    return;
}
```

## 2. Strassen's algorithm for matrix multiplication

The square matrix multiply algorithm is implemented below.

```c
#include <stdlib.h>

int* square_matrix_multiply(int *a, int *b, int d)
{
    int i, j, k;
    int *c = malloc(sizeof(int)*d*d);
    for (i = 0; i < d; ++i)
        for (j = 0; j < d; ++j)
        {
            int index_c = d * i + j;
            *(c+ index_c) = 0;
            for (k = 0; k < d; ++k)
            {
                int index_a = d * i + k, index_b = d * k + j;
                *(c + index_c) += *(a + index_a) * *(b + index_b);
            }
        }
    return c;
}
```

The simple divide-and-conquer version is implemented below.

```c
#include <stdlib.h>

int* square_matrix_multiply_recursive(
        int *a, int ax1, int ay1, int ax2, int ay2,
        int *b, int bx1, int by1, int bx2, int by2, int d)
{
    int n = ax2 - ax1 + 1;
    int *c = malloc(sizeof(int)*n*n);
    if (n == 1) {
        *c = *(a + (ax1 * d + ay1)) * *(b + (bx1 *d + by1));
    }
    else
    {
        int m = n / 2;
        int a11x1 = ax1, a11y1 = ay1, a11x2 = ax1+m-1, a11y2 = ay1+m-1;
        int a12x1 = ax1, a12y1 = ay1+m, a12x2 = ax1+m-1, a12y2 = ay2;
        int a21x1 = ax1+m, a21y1 = ay1, a21x2 = ax2, a21y2 = ay1+m-1;
        int a22x1 = ax1+m, a22y1 = ay1+m, a22x2 = ax2, a22y2 = ay2;
        int b11x1 = bx1, b11y1 = by1, b11x2 = bx1+m-1, b11y2 = by1+m-1;
        int b12x1 = bx1, b12y1 = by1+m, b12x2 = bx1+m-1, b12y2 = by2;
        int b21x1 = bx1+m, b21y1 = by1, b21x2 = bx2, b21y2 = by1+m-1;
        int b22x1 = bx1+m, b22y1 = by1+m, b22x2 = bx2, b22y2 = by2;
        int *c111 = square_matrix_multiply_recursive(
                a, a11x1, a11y1, a11x2, a11y2,
                b, b11x1, b11y1, b11x2, b11y2, d);
        int *c112 = square_matrix_multiply_recursive(
                a, a12x1, a12y1, a12x2, a12y2,
                b, b21x1, b21y1, b21x2, b21y2, d);
        int *c121 = square_matrix_multiply_recursive(
                a, a11x1, a11y1, a11x2, a11y2,
                b, b12x1, b12y1, b12x2, b12y2, d);
        int *c122 = square_matrix_multiply_recursive(
                a, a12x1, a12y1, a12x2, a12y2,
                b, b22x1, b22y1, b22x2, b22y2, d);
        int *c211 = square_matrix_multiply_recursive(
                a, a21x1, a21y1, a21x2, a21y2,
                b, b11x1, b11y1, b11x2, b11y2, d);
        int *c212 = square_matrix_multiply_recursive(
                a, a22x1, a22y1, a22x2, a22y2,
                b, b21x1, b21y1, b21x2, b21y2, d);
        int *c221 = square_matrix_multiply_recursive(
                a, a21x1, a21y1, a21x2, a21y2,
                b, b12x1, b12y1, b12x2, b12y2, d);
        int *c222 = square_matrix_multiply_recursive(
                a, a22x1, a22y1, a22x2, a22y2,
                b, b22x1, b22y1, b22x2, b22y2, d);
        int i, j;
        for (i = 0; i < m; ++i)
            for (j = 0; j < m; ++j)
            {
                *(c+(i*n+j)) = *(c111+(i*m+j)) + *(c112+(i*m+j));
                *(c+(i*n+j+m)) = *(c121+(i*m+j)) + *(c122+(i*m+j));
                *(c+((i+m)*n+j)) = *(c211+(i*m+j)) + *(c212+(i*m+j));
                *(c+((i+m)*n+j+m)) = *(c221+(i*m+j)) + *(c222+(i*m+j));
            }
    }
    return c;
}
```

The Strassen's method is implemented below.

```c
#include <stdlib.h>

int* square_matrix_multiply_strassens_method(
        int *a, int ax1, int ay1, int ax2, int ay2, int d1,
        int *b, int bx1, int by1, int bx2, int by2, int d2)
{
    int n = ax2 - ax1 + 1;
    int *c = malloc(sizeof(int)*n*n);
    if (n == 1) {
        *c = *(a + (ax1 * d1 + ay1)) * *(b + (bx1 *d2 + by1));
    }
    else
    {
        int m = n / 2;
        int a11x1 = ax1, a11y1 = ay1, a11x2 = ax1+m-1, a11y2 = ay1+m-1;
        int a12x1 = ax1, a12y1 = ay1+m, a12x2 = ax1+m-1, a12y2 = ay2;
        int a21x1 = ax1+m, a21y1 = ay1, a21x2 = ax2, a21y2 = ay1+m-1;
        int a22x1 = ax1+m, a22y1 = ay1+m, a22x2 = ax2, a22y2 = ay2;
        int b11x1 = bx1, b11y1 = by1, b11x2 = bx1+m-1, b11y2 = by1+m-1;
        int b12x1 = bx1, b12y1 = by1+m, b12x2 = bx1+m-1, b12y2 = by2;
        int b21x1 = bx1+m, b21y1 = by1, b21x2 = bx2, b21y2 = by1+m-1;
        int b22x1 = bx1+m, b22y1 = by1+m, b22x2 = bx2, b22y2 = by2;

        int *s1 = malloc(sizeof(int)*m*m);
        int *s2 = malloc(sizeof(int)*m*m);
        int *s3 = malloc(sizeof(int)*m*m);
        int *s4 = malloc(sizeof(int)*m*m);
        int *s5 = malloc(sizeof(int)*m*m);
        int *s6 = malloc(sizeof(int)*m*m);
        int *s7 = malloc(sizeof(int)*m*m);
        int *s8 = malloc(sizeof(int)*m*m);
        int *s9 = malloc(sizeof(int)*m*m);
        int *s10 = malloc(sizeof(int)*m*m);
        int i, j;
        for (i = 0; i < m; ++i)
            for (j = 0; j < m; ++j)
            {
                *(s1+(i*m+j)) = *(b+(b12x1+i)*d2+b12y1+j) - *(b+(b22x1+i)*d2+b22y1+j);
                *(s2+(i*m+j)) = *(a+(a11x1+i)*d1+a11y1+j) + *(a+(a12x1+i)*d1+a12y1+j);
                *(s3+(i*m+j)) = *(a+(a21x1+i)*d1+a21y1+j) + *(a+(a22x1+i)*d1+a22y1+j);
                *(s4+(i*m+j)) = *(b+(b21x1+i)*d2+b21y1+j) - *(b+(b11x1+i)*d2+b11y1+j);
                *(s5+(i*m+j)) = *(a+(a11x1+i)*d1+a11y1+j) + *(a+(a22x1+i)*d1+a22y1+j);
                *(s6+(i*m+j)) = *(b+(b11x1+i)*d2+b11y1+j) + *(b+(b22x1+i)*d2+b22y1+j);
                *(s7+(i*m+j)) = *(a+(a12x1+i)*d1+a12y1+j) - *(a+(a22x1+i)*d1+a22y1+j);
                *(s8+(i*m+j)) = *(b+(b21x1+i)*d2+b21y1+j) + *(b+(b22x1+i)*d2+b22y1+j);
                *(s9+(i*m+j)) = *(a+(a11x1+i)*d1+a11y1+j) - *(a+(a21x1+i)*d1+a21y1+j);
                *(s10+(i*m+j)) = *(b+(b11x1+i)*d2+b11y1+j) + *(b+(b12x1+i)*d2+b12y1+j);
            }

        int *p1 = square_matrix_multiply_strassens_method(
                a, a11x1, a11y1, a11x2, a11y2, d1,
                s1, 0, 0, m-1, m-1, m);
        int *p2 = square_matrix_multiply_strassens_method(
                s2, 0, 0, m-1, m-1, m,
                b, b22x1, b22y1, b22x2, b22y2, d2);
        int *p3 = square_matrix_multiply_strassens_method(
                s3, 0, 0, m-1, m-1, m,
                b, b11x1, b11y1, b11x2, b11y2, d2);
        int *p4 = square_matrix_multiply_strassens_method(
                a, a22x1, a22y1, a22x2, a22y2, d1,
                s4, 0, 0, m-1, m-1, m);
        int *p5 = square_matrix_multiply_strassens_method(
                s5, 0, 0, m-1, m-1, m,
                s6, 0, 0, m-1, m-1, m);
        int *p6 = square_matrix_multiply_strassens_method(
                s7, 0, 0, m-1, m-1, m,
                s8, 0, 0, m-1, m-1, m);
        int *p7 = square_matrix_multiply_strassens_method(
                s9, 0, 0, m-1, m-1, m,
                s10, 0, 0, m-1, m-1, m);
        for (i = 0; i < m; ++i)
            for (j = 0; j < m; ++j)
            {
                *(c+(i*n+j)) = *(p5+(i*m+j)) + *(p4+(i*m+j)) - *(p2+(i*m+j)) + *(p6+(i*m+j));
                *(c+(i*n+j+m)) = *(p1+(i*m+j)) + *(p2+(i*m+j));
                *(c+((i+m)*n+j)) = *(p3+(i*m+j)) + *(p4+(i*m+j));
                *(c+((i+m)*n+j+m)) = *(p5+(i*m+j)) + *(p1+(i*m+j)) - *(p3+(i*m+j)) - *(p7+(i*m+j));
            }
    }
    return c;
}
```

## 3. The substitution method for solving recurrences

The **substitution method** comprises two steps:

* Guess the form of the solution.
* Use mathematical induction to find the constants and show that the solution
  works.

**Subtle skill**: Sometimes you might correctly guess the solution of a recurrence,
but some how the math fails to work out.
For example, consider the recurrence,

\[T(n) = T(\lfloor n/2 \rfloor) + T(\lceil n/2 \rceil) + 1. \]

We guess the solution is \\(T(n) = O(n)\\).
We obtain,

\[T(n) \leq c\lfloor n/2 \rfloor + c\lceil n/2 \rfloor + 1 = cn + 1,\]

which does not implies \\(T(n) \leq cn\\).
If our guess is \\(T(n) \leq cn - d\\), we have

\[T(n) \leq cn -2d + 1.\]

which implies that \\(T(n) \leq cn -d\\) as long as \\(d \geq 1\\).

## 4. The recursion-tree method for solving recurrences

A recursion tree is usually used to generate a good guess.
You can then verify by the substitution method.
When using a recursion tree to generate a good guess,
you can often tolerate a small amount of "sloppiness".

## 5. The master method for solving recurrences

Let \\(a \geq 1\\) and \\(b > 1\\) be constants,
let \\(f(n)\\) be a function,
and let \\(T(n)\\) be defined on the nonnegative integers by the recurrence
\[T(n) = aT(n/b) + f(n),\]
where we interpret \\(n/b\\) to mean either \\(\lfloor n/b \rfloor\\)
or \\(\lceil n/b \rceil\\).
Then \\(T(n)\\) has the following asymptotic bounds:

* If \\(f(n) = O(n^{\log_b a - \epsilon})\\) for some constant \\(\epsilon > 0\\),
  then \\(T(n) = \Theta(n^{\log_b a})\\).
* If \\(f(n) = \Theta(n^{\log_b a})\\),
  then \\(T(n) = \Theta(n^{\log_b a}\lg n)\\).
* If \\(f(n) = \Omega(n^{\log_b a + \epsilon})\\) for some constant \\(\epsilon > 0\\),
  and if \\(af(n/b) \leq cf(n)\\) for some constant \\(c < 1\\)
  and all sufficiently large \\(n\\),
  then \\(T(n) = \Theta(f(n))\\).
