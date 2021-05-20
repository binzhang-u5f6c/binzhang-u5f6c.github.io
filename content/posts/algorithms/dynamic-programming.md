---
title: "Dynamic Programming"
date: 2021-04-15T19:25:45+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

## 1. Rod cutting

Three versions of rod cutting algorithms are implemented below.

```c
#include <stdlib.h>

int cut_rod(int *p, int n)
{
    if (n == 0)
        return 0;
    int i, q = -0x7fffffff;
    for (i = 0; i < n; ++i)
    {
        int temp_q = p[i] + cut_rod(p, n-i-1);
        q = temp_q > q ? temp_q : q;
    }
    return q;
}

int memoized_cut_rod_aux(int *p, int n, int *r)
{
    if (n == 0)
        return 0;
    if (r[n-1] >= 0)
        return r[n-1];
    int i, q = -0x7fffffff;
    for (i = 0; i < n; ++i)
    {
        int temp_q = p[i] + memoized_cut_rod_aux(p, n-i-1, r);
        q = temp_q > q ? temp_q : q;
    }
    r[n-1] = q;
    return q;
}

int memoized_cut_rod(int *p, int n)
{
    int i, *r = malloc(sizeof(int)*n);
    for (i = 0; i < n; ++i)
        r[i] = -0x7fffffff;
    int result = memoized_cut_rod_aux(p, n, r);
    free(r);
    return result;
}

int bottom_up_cut_rod(int *p, int n)
{
    int i, j, *r = malloc(sizeof(int)*n);
    for (i = 0; i < n; ++i)
    {
        int q = -0x7fffffff;
        for (j = 0; j <= i; ++j)
        {
            int temp_q = j == i ? p[j] : p[j] + r[i-j-1];
            q = temp_q > q ? temp_q : q;
        }
        r[i] = q;
    }
    int result = r[n-1];
    free(r);
    return result;
}
```

## 2. Matrix-chain multiplication

Four steps to use dynamic programming:

* Characterize the structure of an optimal solution.
* Recursively define the value of an optimal solution.
* Compute the value of an optimal solution.
* Construct an optimal solution from computed information.

## 3. Elements of dynamic programming

There are two key ingredients that an optimization problem must in order
for dynamic programming to apply:

* **Optimal substructure**:
  an optimal solution contains within it optimal solution to subproblems.
* **Overlapping subproblems**:
  a recursive algorithm revisits the same subproblems repeatly.

## 4. Longest common subsequence

A program to find the longest common subsequence of two strings
is implemented below.

```c
#include <stdio.h>
#include <stdlib.h>

void lcs_length(char *x, int nx, char *y, int ny, int *c, int *aux_c)
{
    int i, j;
    for (i = 0; i <= nx; ++i)
        c[i*(ny+1)] = 0;
    for (j = 0; j <= ny; ++j)
        c[j] = 0;
    for (i = 1; i <= nx; ++i)
        for (j = 1; j <= ny; ++j)
            if (x[i-1] == y[j-1])
            {
                c[i*(ny+1)+j] = c[(i-1)*(ny+1)+(j-1)] + 1;
                aux_c[i*(ny+1)+j] = 0;
            }
            else if (c[i*(ny+1)+(j-1)] > c[(i-1)*(ny+1)+j])
            {
                c[i*(ny+1)+j] = c[i*(ny+1)+(j-1)];
                aux_c[i*(ny+1)+j] = -1;
            }
            else
            {
                c[i*(ny+1)+j] = c[(i-1)*(ny+1)+j];
                aux_c[i*(ny+1)+j] = 1;
            }
    return;
}

void print_lcs_aux(char *y, int ny, int *aux_c, int i, int j)
{
    if (i == 0 || j == 0)
        return;
    if (aux_c[i*(ny+1)+j] == 0)
    {
        print_lcs_aux(y, ny, aux_c, i-1, j-1);
        printf("%c", y[j-1]);
    }
    else if (aux_c[i*(ny+1)+j] == 1)
        print_lcs_aux(y, ny, aux_c, i-1, j);
    else
        print_lcs_aux(y, ny, aux_c, i, j-1);
    return;
}

void print_lcs(char *x, int nx, char *y, int ny)
{
    int *c = malloc(sizeof(int)*(nx+1)*(ny+1));
    int *aux_c = malloc(sizeof(int)*(nx+1)*(ny+1));
    lcs_length(x, nx, y, ny, c, aux_c);
    print_lcs_aux(y, ny, aux_c, nx, ny);
    printf("\n");
    free(c); free(aux_c);
    return;
}
```
