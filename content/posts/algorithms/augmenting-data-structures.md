---
title: "Augmenting Data Structures"
date: 2021-04-08T19:41:17+08:00
draft: false
toc: true
math: true
tags:
  - algorithms
---

## 1. Dynamic order statistics

Via adding a new attribute `size` in a red-black tree,
we can determine any order statistic in \\(O(n)\\) time.
The `size` refer to the number of nodes of the subtree
whose root is the node.
It is called **order-statistic tree**.

## 2. How to augment a data structure

* Choose an underlying data structure.
* Determine additional information to maintain
  in the underlying data structure.
* Verify that we can maintain the additional information
  for the basic modifying operations on the underlying data structure.
* Develop new operations.
