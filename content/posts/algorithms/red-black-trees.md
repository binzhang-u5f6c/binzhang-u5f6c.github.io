---
title: "Red-Black Trees"
date: 2021-04-01T19:45:29+08:00
draft: true
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

An interger red-black is implemented below.

## 3. Other balanced search trees

The idea of balancing a search tree is due to AVL trees in 1962.
Another class of search trees, called 2-3 trees was introduced in 1970.
Skip lists provide an alternative to balanced search trees.
