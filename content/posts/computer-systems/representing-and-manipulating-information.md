---
title: "Representing and Manipulating Information"
date: 2020-05-13T20:58:25+08:00
draft: true
toc: true
mathjax: true
tags:
  - computer systems
---

Modern computers store and process information represented as binary digits,
also known as **bits**.

## 1. Information storage

Most computers use blocks of 8 bits, **bytes**, as the smallest addressable
unit of memory.

A long stream of 0/1 is too verbose.
We use hexadecimal to denote binary numbers.

Every computer has a **word size**, indicating the nominal size of pointer
data.

There are two common conventions to order the bytes representing an object.
For 0x01234567:

* big endian: 01 23 45 67
* little endian: 67 45 23 01

There are four operations of **Boolean algebra**: NOT, AND, OR, XOR.

## 2. Interger representations

Unsigned encodings:

\\[ B2U_w(x) = \sum_{i=0}^{w-1} x_i 2^i \\]

The range of values that can be represented using \\(w\\) bits is \\(0\\) to \\(2^w-1\\).

Two's-Complement encodings:

\\[ B2T_w(x) = -x_{w-1}2^{w-1} + \sum_{i=0}^{w-2} x_i 2^i \\]

The range of values that can be represented using \\(w\\) bits is
\\(-2^{w-1}\\) to \\(2^{w-1}-1\\).

|    |8   |16    |32        |64                |
|----|----|------|----------|------------------|
|UMAX|0XFF|0XFFFF|0XFFFFFFFF|0XFFFFFFFFFFFFFFFF|
|TMIN|0X80|0X8000|0X80000000|0X8000000000000000|
|TMAX|0X7F|0X7FFF|0X7FFFFFFF|0X7FFFFFFFFFFFFFFF|
|-1  |0XFF|0XFFFF|0XFFFFFFFF|0XFFFFFFFFFFFFFFFF|
|0   |0X00|0X0000|0X00000000|0X0000000000000000|

One of convenient of Two's-Complement is that expansion or truncating of a
two's-complement number is easy to do via sign extension and dropping the
high-order bits.

## 3. Interger Arithmetic

### 3.1 Unsigned Additon

**PRINCIPLE**: Let \\(0 \leq x, y < 2^w\\),
\\[x + y = (x + y) \mod 2^w.\\]

**PRINCIPLE**: \\(s = x + y\\) overflowed if and only if \\(s < x\\)
(or equivalently, \\(s < y\\)).

**PRINCIPLE**: The negation of an unsigned number \\(x\\) is that
\\[-x = 2^w - x.\\]

### 3.2 Two's-Complement Additon

**PRINCIPLE**: The \\(w\\)-bit two's-complement addition has the exact same
bit-level representation as unsigned addition.

|   x|   y| x+y|   x|   y| x+y|
|----|----|----|----|----|----|
|   4|   4|  -8|  -4|  -5|   7|
|0100|0100|1000|1100|1011|0111|
|   4|   4|   8|  12|  11|   7|

**Note**: This can be the second convenient of Two's-complement encoding.

**Note**: The modular addition forms an **abelian group**.
It's commutative and associative.
0 is its identity element.
The inverse of \\(x (x \neq 0)\\) is \\(2^w - x\\),
which implies that the inverse of \\(2^{w-1}\\) is itself.

**PRINCIPLE**: \\(s = x + y\\) has positive overflow if and only if
\\(x > 0, y > 0\\), but \\(s \leq 0\\).
\\(s\\) has negetive overflow if and only if
\\(x < 0, y < 0\\), but \\(s \geq 0\\).

### 3.3 Two's-Complement Negation

The third convenient of Two's-complement encoding is that the negation has the
exact same bit-level representation as unsigned negation.

There are several clever ways to calculate Two's-Complement negation.
One technique is,
\\[-x = \sim x+1.\\]

### 3.4 Unsigned Multiplication

**PRINCIPLE**: Let \\(0 \leq x, y < 2^w\\),
\\[x * y = (x * y) \mod 2^w.\\]

### 3.5 Two's-Complement Multiplication

**PRINCIPLE**: Let \\(-2^{w-1} \leq x, y < 2^{w-1}\\),
\\[x * y = U2T_w((x * y) \mod 2^w).\\]

**PRINCIPLE**: The \\(w\\)-bit two's-complement multiplication has the exact
same bit-level representation as unsigned multiplication.

### 3.6 Multiplying by Constants

**PRINCIPLE**: Shifting left \\(x << k\\) yields the multiplication by
\\(2^k\\).

Using this principle,
we can compute multiplications with only additions and shifting left.

### 3.7 Dividing by Powers of 2

Integer division always rounds towards zero.
Thus there are differences between positive and negative numbers.

**PRINCIPLE**:

* For unsigned numbers,
  shifting right \\(x >> k\\) yields the division by \\(2^k\\).
* For positive two's-complement numbers,
  arithmetic shifting right \\(x >> k\\) yields the division by \\(2^k\\).
* For negative two's-complement numbers,
  \\((x + ((1 << k) - 1)) >> k\\) yields the division by \\(2^k\\).

## 4. Floating Point

### 4.1 Fractional Binary Numbers

The fractional binary number \\(b_mb_{-1}\dots b_1b_0.b_{-1}b_{-2}\dots
b_{-n}\\) represents the number \\(\sum_{i=-n}^{m} 2^i b_i\\).

### 4.2 IEEE Floating-Point Representation

The IEEE floating-point representation is divided into three fields

* a single bit encoding the sign s
* the k-bit field encoding the exponent E
* the n-bit field encoding the significant M

The number is \\((-1)^s \times M \times 2^E\\).

**Normalized Values**:
If E is neither all zeros nor all ones,

\\[E = e - (2^{k-1} - 1)\\]

where e is the unsigned number represented by E.

\\[M = 1.b_1b_2\dots b_n\\]

**Denormalized Values**:
If E is all zeros,

\\[E = 2-2^{k-1}\\]
\\[M = 0.b_1b_2\dots b_n\\]

Denormalized values serv two purposes:

* they provide a way to represent 0
* they are to represent numbers very close to 0

**Special Values**:
If E is all ones and M is all zeros,
the resulting values represent infinity,
\\(+\infty\\) or \\(-\infty\\).
If E is all ones and M is not all zeros,
the resulting values represent a NaN,
short for "not a number".

### 4.3 Example Numbers

| description    | representation   |
|----------------|------------------|
|zero            | 0 00...00 0...00 |
|smallest number | 0 00...00 0...01 |
|one             | 0 01...11 0...00 |
|largest number  | 0 11...10 1...11 |

One interesting property of this representation is that,
the positive values increase as the unsigned integers
which have the same bit level representation
increase.
The IEEE floating-point representation was designed
so that floating-point numbers can be sorted using integer sorting routine.

### 4.4 Rounding

The IEEE floating-point format defines four rounding modes:

* round-to-even
* round-toward-zero
* round-down
* round-up

**Note**: Round-to-even seems odd at first,
but it can avoid the statistical bias
when rounding a set of data values.

### 4.5 Floating-Point Operations

The addition and multiplication are not associative due to rounding
or the possibility of overflow.
