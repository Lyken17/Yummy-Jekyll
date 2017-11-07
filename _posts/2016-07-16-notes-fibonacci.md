---
layout: post
title: How to Compute Fibonacci Sequence Quickly
categories: Algorithm ACM-ICPC
description: Notes for Fibonacci Sequence
keywords: Fibonacci, Algorithm, Note
use_math: true
no-post-nav: true
---
As one of the common sequence, fibonacci sequence plays important role on many applications.

# Definition
Fibonacci sequence is defined as follow
$$ F_{n} = F_{n-1} + F_{n-2} $$

# Most naive way $O(n^2)$

```python
# Python
def fibonacci(n):
    if n == 0 or n == 1:
        return 1
    return fibonacci(n-1) + fibonacci(n-2)
```

# Improved by memorisation : $O(n)$


```python
# Python
# Recursive
saved = [-1] * (n + 1) # Initialise an array of length n
def fibonacci(n):
    if n == 0 or n == 1:
        return saved[n] = 1
    if saved[n] != -1: # it has been computed
	    return saved[n]
    else:
	    return fibonacci(n-1) + fibonacci(n-2)
```

```python
# Python
# Iterative
saved = [0] * n # Initialise an array of length n
saved[0] = saved[1] = 1
for i in xrange(2, n):
	saved[i] = saved[i-1] + saved[i-2]

def fibonacci(n):
	return saved[n]
```

# Quick solution 1 for large N

Exact formula can be infered fibonacci recursive formula.

$$ F_{n} = F_{n-1} + F_{n-2} $$

$$ F_{n} + bF_{n-1} = a(F_{n-1} + bF_{n-2})$$

We have

$$ b - a = 1 \\
   a * b = 1 $$

$$ a = \frac{\sqrt{5} + 1}{2}, b = \frac{\sqrt{5} - 1}{2} $$

Let $G_{n} = F_{n+1} + \frac{\sqrt{5} - 1}{2} F_{n}$. We can see $G(n)$ is a geometric sequence that $G_{n} = \frac{\sqrt{5} + 1}{2} G_{n-1}$. It is easy to compute $G_{n}$, and here comes the exact formula

$$ F_{n} = \frac{(\frac{\sqrt{5} + 1}{2})^{n} - (\frac{\sqrt{5} - 1}{2})^{n}}{\sqrt{5}}$$

[Exponention by squaring](https://en.wikipedia.org/wiki/Exponentiation_by_squaring) has time complexity of $O(lgn)$, thus by given exact formula, $N^{th}$ number in fibonacci sequence can be computed in $O(lgn)$ as well.

```python
# Python
from math import sqrt
def fast_power(base, exponent):
    res = 1.0
    while exponent > 0:
        if exponent & 1:
            res = res * base
        exponent >>= 1
        base = base * base
    return res

def fibonacci(n):
    return (fast_power((sqrt(5) + 1.0) / 2.0, n) - \
            fast_power(((-sqrt(5) + 1.0) / 2.0), n)) \
               / sqrt(5)
```

## thinkings about quick solution1
### problem of precision
However, this formula has a irrational number of infinite length. Python has number type of arbitary precision but C++/Java don't. In fact, float number in python is not infinitely long either. There are two approaches to avoid it.

1. Symbolic Computation

    [Symbolic computation](https://en.wikipedia.org/wiki/Computer_algebra) can manipulate mathetical expression containing variables that have no given value and are manipulated as symbols. By which means it can get the **EXACT VALUE** of a formula.

    ```python
    #Python
    def fibo(n):
        from sympy import simplify, sqrt, N
        res = simplify(((sqrt(5) + 1.0) / 2.0) ** n) \
        	- simplify(((-sqrt(5) + 1.0) / 2.0) ** n)
        res = N(simplify(res / sqrt(5)))
        return res
    ```

2. matrix multiplication

    Back to fibonacci's definition: $ F_{n} = F_{n-1} + F_{n-2} $, instead of computing its exact form, we re-write it in matrix form

    Notice, in matrix muptiplication, only integers need to manipulated, needless to worry about floating error caused by irrational number.

    ```python
    #python
    import numpy as np

    def fast_power(base, exponent):
        res = np.matrix([[1, 0],[0, 1]])
        while exponent > 0:
            if exponent & 1:
                res = res * base
            exponent >>= 1
            base = base * base
        return res

    trans = np.matrix([[1, 1],[1, 0]])
    value = np.matrix([[1, 0]]).T

    def fibonacci(n):
        return (fast_power(trans, n) * value)[1,0]
    ```
