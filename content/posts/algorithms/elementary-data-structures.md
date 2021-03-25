---
title: "Elementary Data Structures"
date: 2021-03-17T19:02:31+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

## 1. Stacks and queues

Stacks and queues are dynamic sets.
The stack implements a **last-in, first-out** policy,
while the queue implements a **first-in, first-out** policy.

An integer stack of size 1024 is implemented below.

```c
#include <stdlib.h>
#include <stdio.h>

struct stack
{
    int a[1024];
    int top;
};

void push(struct stack *s, int x)
{
    if (s->top == 1023)
    {
        fprintf(stderr, "Overflow!\n");
        exit(1);
    }
    ++s->top;
    s->a[s->top] = x;
    return;
}

int pop(struct stack *s)
{
    if (s->top == -1)
    {
        fprintf(stderr, "Underflow!\n");
        exit(1);
    }
    --s->top;
    return s->a[s->top+1];
}
```

An integer queue of size 1024 is implemented below.

```c
#include <stdlib.h>
#include <stdio.h>

struct queue
{
    int a[1024];
    int head, tail;
};

void enqueue(struct queue *q, int x)
{
    q->a[q->tail] = x;
    q->tail = (q->tail + 1) % 1024;
    if (q->tail == q->head)
    {
        fprintf(stderr, "Overflow!\n");
        exit(1);
    }
    return;
}

int dequeue(struct queue *q)
{
    if (q->head == q->tail)
    {
        fprintf(stderr, "Underflow!\n");
        exit(1);
    }
    int x = q->a[q->head];
    q->head = (q->head + 1) % 1024;
    return x;
}
```

## 2. Linked lists

A **linked list** is a linear-arranged data structure
in which the order is determined by a pointer in each object.

An integer double linked list with a sentinel is implemented below.

```c
struct element
{
    int key;
    struct element *prev, *next;
};

struct linked_list
{
    struct element *sentinel;
};

struct element* list_search(struct linked_list *L, int k)
{
    struct element *re = L->sentinel->next;
    while (re != L->sentinel && re->key != k)
        re = re->next;
    return re;
}

void list_insert(struct linked_list *L, struct element *x)
{
    x->next = L->sentinel->next;
    L->sentinel->next->prev = x;
    x->prev = L->sentinel;
    L->sentinel->next = x;
    return;
}

void list_delete(struct linked_list *L, struct element *x)
{
    x->prev->next = x->next;
    x->next->prev = x->prev;
    return;
}
```

## 3. Implementing pointers and objects

We can use three arrays to implement a double linked list.
For an index `i`, `key[i]`, `next[i]` and `prev[i]` represent an object
in the double linked list.

A single array can also be used to implement a linked list
just like how operating systems manage the computer memory.
We can use a linked list with only `next` attribute to implement a stack,
which we call the **free list**, to record which objects are free.

## 4. Representing rooted trees

The representation of a binary is just like that of a linked list.
The difference is objects of trees have two childs.
