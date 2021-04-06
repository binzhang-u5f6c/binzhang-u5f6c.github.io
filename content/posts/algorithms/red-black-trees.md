---
title: "Red-Black Trees"
date: 2021-04-01T19:45:29+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

## 1. Properties of red-black trees

A **red-black tree** is a binary search tree with one extra attribute per node:
its color, which can be either RED or BLACK.

As a matter of convenience,
we introduce a single sentinel and all leaf nodes point to this sentinel.
A red-black tree satisfies the following properties:

* Every node is either red or black.
* The root is black.
* The sentinel is black.
* If a node is red, then both its children are black.
* For each node, all simple paths from the node to the sentinel
  contain the same number of black nodes.

Thus red-black trees ensure that
no such path is more than twice as long as any other,
so that the tree is **balanced**.
A red-black tree with \\(n\\) nodes has height at most \\(2\log(n+1)\\).

## 2. Rotation, insertion and deletion

To keep the properties while insertion and deletion,
new operation **rotation** is necessary.

An interger red-black tree is implemented below.

```c
#include <stdlib.h>

struct object
{
    int key;
    char color;
    struct object *p, *left, *right;
};

struct red_black_tree
{
    struct object *root, *sentinel;
};

/***********
*  search  *
***********/

struct object *rb_tree_search(struct object *x, int k)
{
    if (x == NULL || k == x->key)
        return x;
    if (k < x->key)
        return rb_tree_search(x->left, k);
    else
        return rb_tree_search(x->right, k);
}

struct object *rb_tree_min(struct object *x)
{
    while (x->left != NULL)
        x = x->left;
    return x;
}

struct object *rb_tree_max(struct object *x)
{
    while (x->right != NULL)
        x = x->right;
    return x;
}

struct object *rb_tree_successor(struct object *x)
{
    if (x->right != NULL)
        return rb_tree_min(x->right);
    struct object *y = x->p;
    while (y != NULL && x == y->right)
        {x = y; y = y->p;}
    return y;
}

struct object *rb_tree_predecessor(struct object *x)
{
    if (x->left != NULL)
        return rb_tree_max(x->left);
    struct object *y = x->p;
    while (y != NULL && x == y->left)
        {x = y; y = y->p;}
    return y;
}

/*************
*  rotation  *
*************/

void left_rotate(struct red_black_tree *T, struct object *x)
{
    struct object *y = x->right;
    x->right = y->left;
    if (y->left != T->sentinel)
        y->left->p = x;
    y->p = x->p;
    if (x->p == T->root)
        T->root = y;
    else if (x->p->left == x)
        x->p->left = y;
    else
        x->p->right = y;
    y->left = x;
    x->p = y;
    return;
}

void right_rotate(struct red_black_tree *T, struct object *x)
{
    struct object *y = x->left;
    x->left = y->right;
    if (y->right != T->sentinel)
        y->right->p = x;
    y->p = x->p;
    if (x->p == T->root)
        T->root = y;
    else if (x->p->left == x)
        x->p->left = y;
    else
        x->p->right = y;
    y->right = x;
    x->p = y;
    return;
}

/**************
*  insertion  *
**************/

void rb_insert_fixup(struct red_black_tree *T, struct object *z)
{
    while (z->p->color == 'r')
    {
        if (z->p == z->p->p->left)
        {
            struct object *y = z->p->p->right;
            if (y->color == 'r')
            {
                z->p->color = 'b';
                y->color = 'b';
                z->p->p->color = 'r';
                z = z->p->p;
            }
            else
            {
                if (z == z->p->right)
                {
                    z = z->p;
                    left_rotate(T, z);
                }
                z->p->color = 'b';
                z->p->p->color = 'r';
                right_rotate(T, z->p->p);
            }
        }
        else
        {
            struct object *y = z->p->p->left;
            if (y->color == 'r')
            {
                z->p->color = 'b';
                y->color = 'b';
                z->p->p->color = 'r';
                z = z->p->p;
            }
            else
            {
                if (z == z->p->left)
                {
                    z = z->p;
                    right_rotate(T, z);
                }
                z->p->color = 'b';
                z->p->p->color = 'r';
                left_rotate(T, z->p->p);
            }
        }
    }
    return;
}

void rb_insert(struct red_black_tree *T, struct object *z)
{
    struct object *x = T->root, *y = T->sentinel;
    while (x != T->sentinel)
    {
        y = x;
        if (z->key < x->key)
            x = x->left;
        else
            x = x->right;
    }
    z->p = y;
    if (y == T->sentinel)
        T->root = z;
    else if (z->key < y->key)
        y->left = z;
    else
        y->right = z;
    z->left = T->sentinel;
    z->right = T->sentinel;
    z->color = 'r';
    rb_insert_fixup(T, z);
    return;
}

/*************
*  deletion  *
*************/

void rb_transplant(struct red_black_tree *T,
                   struct object *u,
                   struct object *v)
{
    if (u->p == T->sentinel)
        T->root = v;
    else if (u == u->p->left)
        u->p->left = v;
    else
        u->p->right = v;
    v->p = u->p;
    return;
}

void rb_delete_fixup(struct red_black_tree *T, struct object *x)
{
    struct object *w;
    while (x != T->sentinel && x->color == 'b')
    {
        if (x == x->p->left)
        {
            w = x->p->right;
            if (w->color == 'r')
            {
                w->color = 'b';
                x->p->color = 'r';
                left_rotate(T, x->p);
                w = x->p->right;
            }
            if (w->left->color == 'b' && w->right->color == 'b')
            {
                w->color = 'r';
                x = x->p;
            }
            else
            {
                if (w->right->color == 'b')
                {
                    w->left->color = 'b';
                    w->color = 'r';
                    right_rotate(T, w);
                    x = x->p->right;
                }
                w->color = x->p->color;
                x->p->color = 'b';
                w->right->color = 'b';
                left_rotate(T, x->p);
                x = T->root;
            }
        }
        else
        {
            w = x->p->left;
            if (w->color == 'r')
            {
                w->color = 'b';
                x->p->color = 'r';
                right_rotate(T, x->p);
                w = x->p->left;
            }
            if (w->left->color == 'b' && w->right->color == 'b')
            {
                w->color = 'r';
                x = x->p;
            }
            else
            {
                if (w->right->color == 'b')
                {
                    w->left->color = 'b';
                    w->color = 'r';
                    left_rotate(T, w);
                    x = x->p->left;
                }
                w->color = x->p->color;
                x->p->color = 'b';
                w->right->color = 'b';
                right_rotate(T, x->p);
                x = T->root;
            }
        }
    }
    x->color = 'b';
    return;
}

void rb_delete(struct red_black_tree *T, struct object *z)
{
    struct object *x, *y;
    y = z;
    char y_original_color = y->color;
    if (z->left == T->sentinel)
    {
        x = z->right;
        rb_transplant(T, z, z->right);
    }
    else if (z->right == T->sentinel)
    {
        x = z->left;
        rb_transplant(T, z, z->left);
    }
    else
    {
        y = rb_tree_min(z->right);
        y_original_color = y->color;
        x = y->right;
        if (y->p != z)
        {
            rb_transplant(T, y, y->right);
            y->right = z->right;
            y->right->p = y;
        }
        rb_transplant(T, z, y);
        y->left = z->left;
        y->left->p = y;
        y->color = z->color;
    }
    if (y_original_color == 'b')
        rb_delete_fixup(T, x);
    return;
}
```

## 3. Other balanced search trees

The idea of balancing a search tree is due to AVL trees in 1962.
Another class of search trees, called 2-3 trees was introduced in 1970.
Skip lists provide an alternative to balanced search trees.
