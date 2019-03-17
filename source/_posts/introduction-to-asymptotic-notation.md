---
title: Introduction to Asymptotic Notation
date: 2019-02-28 12:05:54
tags: 
- Algorithm
- Time complexity
thumbnail: https://i.imgur.com/K6F6Ojx.jpg
---




# Asymptotic Notation

## Big-O: $O$


{% raw %}
$$
O(g(n)) = \left \{ f(n) \space | \space \exists c, n_0 > 0, \space s.t. f(n) \leq c \cdot g(n) \space \space , \forall n \geq n_0 \right \}
$$
{% endraw %}



<img alt="bigO" src="https://i.imgur.com/9hS03jF.png" width="60%"/>

## Omega: $\Omega$

{% raw %}
$$
\Omega (g(n)) = \left \{ f(n) \space | \space \exists c, n_0 > 0, \space s.t. f(n) \geq c \cdot g(n) \space \space ,  \forall n \geq n_0 \right \}
$$ 
{% endraw %}


## Theta: $\Theta$

{% raw %}
$$
\Theta (g(n)) = \left \{ f(n) \space | \space \exists c_1, c_2, n_0, \space s.t. c_1 \cdot g(n) \leq f(n) \leq c_2 \cdot g(n) \space \space , \forall n \geq n_0  \right \}
$$
{% endraw %}

<!-- more -->



> 相對$O$與$\Omega$更嚴謹地界定演算法的時間複雜度

## little-o: $o$

{% raw %}
$$
o(g(n)) = \left \{ f(n) \space | \space  \forall c > 0, \exists n_0 > 0, \space s.t. f(n) < c \cdot g(n) \space \space , \forall n \geq n_0 \right \}
$$ 
{% endraw %}

## little-omega: $\omega$

{% raw %}
$$
\omega (g(n)) = \left \{ f(n) \space | \space  \forall c > 0, \exists n_0 > 0, \space s.t. f(n) > c \cdot g(n) \space \space , \forall n \geq n_0 \right \}
$$ 
{% endraw %}



---


# Properties

## Transitivity

$f(n) \in O(g(n)), \space g(n) \in O(h(n))$

then $f(n) \in O(h(n))$

> True if $O、 \Omega 、 \Theta 、 o 、 \omega$

### Prove that $f(n) \in O(g(n)), \space g(n) \in O(h(n)) \Rightarrow f(n) \in O(h(n))$

$\because f(n) \in O(g(n)) \therefore \exists c_1, n_1, s.t. f(n) \leq c_1 g(n), \space \forall n \geq n_1$

$\because g(n) \in O(h(n)) \therefore \exists c_2, n_2, s.t. g(n) \leq c_2 h(n), \space \forall n \geq n_2$

where $n \geq max(n_1, n_2)$, let $max(n_1, n_2) = n_0$

$f(n) \leq c_1 g(n) \leq c_1 \cdot c_2 h(n)$, let $c_1 \cdot c_2 = c > 0$

$\Rightarrow f(n) \leq c \cdot h(n) \space \forall n \geq n_0$

$\therefore f(n) \in O(h(n))$

### Prove that $f(n) \in o(g(n)), g(n) \in o(h(n)) \Rightarrow f(n) \in o(h(n))$

> 需先定出$c_1, c_2$否則$n_1, n_2$會隨$c_1, c_2$變化，所以不能用$Big-O$的方法證明

Want to prove: $f(n) \in o(h(n))$
Need: $\forall c >0$, let $c_1 = 2, c_2 = \frac{C}{2}$ (只要$c_1 \times c_2 = c$即可)

$f(n) \in o(g(n)) \Rightarrow \exists n_1 > 0, \space s.t. \space f(n) \leq c_1 g(n), \space \forall n \geq n_1$ 

**其中$n_1$是$c_1$的函數 $\Rightarrow n_1(c_1)$，這也是需要固定$c_1、c_2$的原因**

$g(n) \in o(h(n)) \Rightarrow \exists n_2 > 0, \space s.t. \space g(n) \leq c_2 h(n), \space \forall n \geq n_2$ 

When $n \geq max(n_1, n_2), \space f(n) \leq c_1 \cdot g(n) \leq c_1 \cdot c_2 h(n)$

let $c_1 \cdot c_2 = c > 0$

$\exists n_0 > 0, \space s.t. f(n) \leq c \cdot h(n), \space \forall n \leq n_0$

$\Rightarrow f(n) \in o(h(n))$

## Reflexivity

$f(n) \in \Theta (f(n))$

## Symmetry

$f(n) \in \Theta (g(n)) \space iff \space g(n) \in \Theta (f(n))$

## Transpose Symmetry

$f(n) \in O(g(n)) \space iff \space g(n) \in \Omega (f(n))$

$f(n) \in o(g(n)) \space iff \space g(n) \in \omega (f(n))$



## Example

### 1. There exists two functions $f(n)、g(n), \space s.t. f(n) \notin O(g(n))$ and $g(n) \notin O(f(n))$


{% raw %}
example: $f(n) = n, \space g(n) = \begin{cases} & 1 \space \space \text{ ,if } n \space is \space even \\ & n^2 \text{ ,if } n \space is \space odd \end{cases}$
{% endraw %}

<img alt="" src="https://i.imgur.com/gJtn9cr.png" width="50%" />




### 2. Disprove that $\frac{1}{100} n\log_{2}{n} \in O(n)$ is false

$\frac{1}{100} n\log_{2}{n} \leq c \cdot n \space \space \forall n \geq n_{0}$

so $\frac{1}{100} \log_{2}{n} \leq c$

$\because$ c is not constant

$\therefore$ $\frac{1}{100} n\log_{2}{n} \notin O(n)$

### 3. If $g(n) \in O(f(n))$ and $g(n) \in \omega (h(n))$ then $f(n) \in \omega (h(n))$

$\because g(n) \in O(f(n))$

$\therefore g(n) \leq c_{1} \cdot f(n) \space \space \forall n \geq n_{0}$

$\because g(n) \in \omega (h(n))$

$\therefore g(n) > c_{2} \cdot h(n) \space \space \forall n \geq n_{0}$

$c_{2} \cdot h(n) < g(n) \leq c_{1} \cdot f(n)$

$\Rightarrow f(n) > c_{3} \cdot h(n) \space \space \forall n \geq n_{0}$ where $c_{3}$ and $n_{0}$ are positive constants

$\Rightarrow f(n) \in \omega (h(n))$ 