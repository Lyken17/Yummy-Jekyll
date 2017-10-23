---
layout: post
title: Gumbel-Max Trick
categories: Machine-Learning Convex-Optimization
description: Notes for Gumbel-Max Trick
keywords: Gumbel-Max, Optimization, Note
use_math: true
no-post-nav: true
---
Notes for Gumbel-Max trick.

# What is Gumbel-Max Trick?
Nowadays people prefer end-to-end fashion in various learning tasks. However, as one of the most common goal, sampling from a discrete distribution does not have a stable gradient. Gumbel-Max trick provides a way to convert sampling into a optimization problem to alleviate unstable gradient.

## The usual way
To sample from discrete distrbution, the common way is to first exponentiate and then normalize.

$$ z = \sum_{i=1}^{n}exp(x_i) $$

$$\pi_k=\frac{exp(x_k)}{z}$$

After getting $n$ bins of probabilities, we then can use an algorithm to sampling.

$$ \theta = uniform(0,1) $$

Find a $k$ that
$$ \sum_{i=1}^{k-1} \pi_k \le (\theta * z) < \sum_{i=1}^{k} \pi_k $$.

Then $k$ is the sampling choice we want.

## Gumbel-Max Trick
Generate $g_1, g_2, ... g_k$ that are i.i.d from gumbel.

$$ g_k \in gumbel(0,1) $$

It can be proved that

$$ y = \underset{i \in {1,2..k}}{argmax} \quad x_i + g_i$$

$y$ is equvilant to previous $k$. And for $argmax$, we can simulate the gradient by adjusting the temperature  

$$ z_i = x_i + g_i$$

$$ y_{onehot} = \frac{exp(z_i / \tau) }{\sum_{i=1}^{n}exp(z_i / \tau)}$$

When the temperature $\tau$ is close the zero, the equation above gives us a close result of onehot. In this approach, all steps are continuos with stable gradient. Thus the original goal is achieved.
