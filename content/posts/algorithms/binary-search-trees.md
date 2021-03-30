---
title: "Binary Search Trees"
date: 2021-03-30T19:43:21+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

## What is a binary search tree?

A **binary search tree** is organized in a binary tree.
The tree is represented by a linked data structure
in which each node is an object.
In addition to a `key` and satellite data,
each object contains 3 more attributes: `left`, `right` and `parent`

The keys in a binary search tree are always stored in such a way as to satify
that for each node in a binary search tree,
the key of any node in its left subtree is smaller than its own key,
and the key of any node in its right subtree is larger than its own key,

## Querying a binary search tree

Besides the SEARCH operation,
binary search trees support queries as MINIMUM, MAXIMUM,
SUCCESSOR and PREDECESSOR.

## Insertion and deletion

To insert a new element to a binary search tree is relatively straightforward,
but handling deletion when the node has both two children is
somewhat more intricate.

An interger binary search tree is implemented below.

```c
#include <stdio.h>
#include <stdlib.h>

struct object
{
    int key;
    struct object *p, *left, *right;
};

struct binary_search_tree
{
    struct object *root;
};

void inorder_tree_walk(struct object *x)
{
    if (x != NULL)
    {
        inorder_tree_walk(x->left);
        printf("%d ", x->key);
        inorder_tree_walk(x->right);
    }
    printf("\n");
    return;
}

struct object *tree_search(struct object *x, int k)
{
    if (x == NULL || k == x->key)
        return x;
    if (k < x->key)
        return tree_search(x->left, k);
    else
        return tree_search(x->right, k);
}

struct object *tree_min(struct object *x)
{
    while (x->left != NULL)
        x = x->left;
    return x;
}

struct object *tree_max(struct object *x)
{
    while (x->right != NULL)
        x = x->right;
    return x;
}

struct object *tree_successor(struct object *x)
{
    if (x->right != NULL)
        return tree_min(x->right);
    struct object *y = x->p;
    while (y != NULL && x == y->right)
        {x = y; y = y->p;}
    return y;
}

struct object *tree_predecessor(struct object *x)
{
    if (x->left != NULL)
        return tree_max(x->left);
    struct object *y = x->p;
    while (y != NULL && x == y->left)
        {x = y; y = y->p;}
    return y;
}

void tree_insert(struct binary_search_tree *T, struct object *z)
{
    struct object *x = T->root, *y = NULL;
    while (x != NULL)
    {
        y = x;
        if (z->key < x->key)
            x = x->left;
        else
            x = x->right;
    }
    z->p = y;
    if (y == NULL)
        T->root = z;
    else if (z->key < y->key)
        y->left = z;
    else
        y->right = z;
    return;
}

void transplant(struct binary_search_tree *T,
                struct object *u,
                struct object *v)
{
    if (u->p == NULL)
        T->root = v;
    else if (u == u->p->left)
        u->p->left = v;
    else
        u->p->right = v;
    if (v != NULL)
        v->p = u->p;
    return;
}

void tree_delete(struct binary_search_tree *T, struct object *z)
{
    if (z->left == NULL)
        transplant(T, z, z->right);
    else if (z->right == NULL)
        transplant(T, z, z->left);
    else
    {
        struct object *y = tree_min(z->right);
        if (y->p != z)
        {
            transplant(T, y, y->right);
            y->right = z->right;
            y->right->p = y;
        }
        transplant(T, z, y);
        y->left = z->left;
        y->left->p = y;
    }
}
```
