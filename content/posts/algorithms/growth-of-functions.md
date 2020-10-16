---
title: "Growth of Functions"
date: 2020-07-20T21:30:08+08:00
draft: false
toc: true
mathjax: true
tags:
  - algorithms
---

## 1. Asymptotic notation

\\(\Theta\\)-notation: \\(f(n) \in \Theta(g(n))\\) if there exist positive
constants \\(c_1, c_2, n_0\\), such that for all \\(n \geq n_0\\),

\\[0 \leq c_1 g(n) \leq f(n) \leq c_2 g(n)\\]

\\(O\\)-notation: \\(f(n) \in O(g(n))\\) if there exist positive constants
\\(c, n_0\\), such that for all \\(n \geq n_0\\),

\\[0 \leq f(n) \leq c g(n)\\]

\\(\Omega\\)-notation: \\(f(n) \in \Omega(g(n))\\) if there exist positive
constants \\(c, n_0\\), such that for all \\(n \geq n_0\\),

\\[0 \leq c g(n) \leq f(n)\\]

\\(o\\)-notation: \\(f(n) \in o(g(n))\\) if for all positive constant \\(c\\),
there exists \\(n_0\\), such that for all \\(n \geq n_0\\),

\\[0 \leq f(n) < c g(n)\\]

\\(\omega\\)-notation: \\(f(n) \in \omega(g(n))\\) if for all positive constant
\\(c\\), there exists \\(n_0\\), such that for all \\(n \geq n_0\\),

\\[0 \leq c g(n) < f(n)\\]

## 2. Standard notations and common functions

### 2.1 Monotonicity

A function \\(f(n)\\) is **monotonically increasing**,
if \\(m \leq n\\) implies \\(f(m) \leq f(n)\\).

A function \\(f(n)\\) is **monotonically decreasing**,
if \\(m \leq n\\) implies \\(f(m) \geq f(n)\\).

### 2.2 Floors and ceilings

\\[x-1 < \lfloor x \rfloor \leq x \leq \lceil x \rceil < x+1\\]
\\[\lceil n/2 \rceil + \lfloor n/2 \rfloor = n\\]
\\[\left\lceil \frac{\lceil x/a \rceil}{b} \right\rceil
    = \left\lceil \frac{x}{ab} \right\rceil\\]
\\[\left\lfloor \frac{\lfloor x/a \rfloor}{b} \right\rfloor
    = \left\lfloor \frac{x}{ab} \right\rfloor\\]
\\[\left\lceil \frac{a}{b} \right\rceil \leq \frac{a+b-1}{b}\\]
\\[\left\lfloor \frac{a}{b} \right\rfloor \geq \frac{a-b+1}{b}\\]

### 2.3 Modular arithmetic

The value of \\(a \mod n\\) is the **residue of the quotient \\(a/n\\)**.

### 2.4 Polynomials

A **polynomial in \\(n\\) of degree \\(d\\)** is a function of the form

\\[p(n) = \sum_{i=0}^d a_i n^i\\]

### 2.5 Exponentials

\\[f(n) = a^n\\]

The Taylor series of \\(e^x\\),

\\[e^x = \sum_{i=0}^\infty \frac{x^i}{i!}\\]

### 2.6 Logarithms

Denote \\(\lg n = \log_2 n\\) and \\(\ln n = \log_e n\\).

### 2.7 Factorials

Stirling's approximation,

\\[n! = \sqrt{2\pi n}(\frac{n}{e})^n(1 + \Theta(\frac{1}{n}))\\]

### 2.8 Functional iteration

\\[f^{(i)}(n) = f(f(\dots f(n)))\\]

### 2.9 The iteration logarithm function

\\[\lg^* n = \min \\{i : \lg^{(i)} n \leq 1\\}\\]
