---
title: Introduction to Asymptotic Notation and Analysis of Algorithms
date: 2019-02-28 12:05:54
tags: 
- Algorithm
- Time complexity
thumbnail: https://i.imgur.com/K6F6Ojx.jpg
---



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
\Theta (g(n)) = \left \{ f(n) \space | \space \exists c_1, c_2, n_0 > 0, \space s.t. c_1 \cdot g(n) \leq f(n) \leq c_2 \cdot g(n) \space \space , \forall n \geq n_0  \right \}
$$
{% endraw %}

<!-- more -->



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

example:

$f(n) = n$

$\space g(n) = \begin{cases} & 1  \text{ , if n is even} \\ & n^2 \text{ , if  n is odd } \end{cases}$

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

# Recurrence Relations

## Substitution Method

1. Guess: $T(k) \leq c \cdot k^4, \space \forall k$, for some const. $c$
2. Verify:
   * Base case: $k = 1$, need $T(1) \leq c \cdot 1^{4} \Rightarrow c \geq 1$
   * Assume true for $k < n$, when $k = n$, $T(n) = 4T( \frac{n}{2} ) + n \leq 4 \left [  c \cdot   \left ( \frac{n}{2}\right )^{4} \right ] + n = c \cdot n^{4} + (- \frac{3}{4} c n^{4} + n) \leq c \cdot n^{4}$成立
     若$(- \frac{3}{4} c n^{4} + n) \leq 0 \Rightarrow c \geq \frac{4}{3}$
     
     $\Rightarrow T(k) \leq c \cdot k^{4} \space \forall c \geq \frac{4}{3}$
     
     $\Rightarrow T(k) = O(k^{4})$
     

     
1. Guess: $T(k) \leq c \cdot k^2, \space \forall k$, for some const. $c$
2. Verify:
   * Base case: $k = 1$, need $T(1) \leq c \cdot 1^2 \Rightarrow c \geq 1$
   * Assume true for $k < n$, when $k = n$, $T(n) = 4T( \frac{n}{2} ) + n \leq \left [  c \cdot   \left ( \frac{n}{2} \right )^{2}  \right ] + n = cn^{2} + n \leq c \cdot n^2$,必不成立，所以修正猜測



1. Guess: $T(k) \leq c_1 k^2 + c_2 k, \space k$ for some const $c_1, c_2$
2. Verify:
   * Base case: $k = 1, \space T(1) \leq c_1 \cdot 1^{2} + c_2 \cdot 1$
     $\Rightarrow need \space c_1 + c_2 \geq 1$
   * Assume true for $k < n$
     when $k = n, \space T(n) = 4T(\frac{n}{2}) + n \leq \left [  c_1 \left (  \frac{n}{2} \right )^{2} + c_2 \left ( \frac{n}{2} \right ) \right ] + n = c_1 n^{2} + c_2 n + \left ( 1 + c_2 \right ) = c_1 n^{2} + c_2 n + \left ( 1 + c_2 \right ) n \leq c_1 n^{2} + c_2 n$
     
     $\Rightarrow 1 + c_2 \leq 0 \Rightarrow c_2 \leq -1$
     
     for some const. $c_1 = 2, c_2 = -1$
     
     $T(n) \leq 2n^{2} - n \in O(n)$
     
## Repeated Substitution

$T(n) = 4T(\frac{n}{2}) + n \space \space \space$ ($k=1$)

$= 4 \left [ 4T(\frac{n}{4})+ \frac{n}{2} \right ] + n \space \space \space$ 

$= 4^{2}T(\frac{n}{4}) + 2n + n  \space \space \space$ ($k=2$)

$= 4^2 \left [ 4T(\frac{n}{8}) + \frac{n}{4} \right] + 2n + n$

$= 4^3 T(\frac{n}{8}) + 4n + 2n + n  \space \space \space$ ($k=3$)

$\vdots$

$= 4^{k}T(\frac{n}{2^{k}}) + (2^{k-1}n+2^{k-2}n+ \dots +2n+n)$

$\frac{n}{2^k} = 1 \Rightarrow n = 2^k$

$\Rightarrow k = log_{2}{n}$

$= n^{2} T(1) + (2^{k-1}+2^{k-2}+ \dots +2+1)n$

$=n^{2}T(1) + \frac{2^{k}-1}{2-1}n$

$= n^{2} \cdot 1 +(2^{k}-1)n$

$=n^2+(n-1)n$

$= 2n^{2} - n \in O(n^{2})$


## Recursion Tree

Repeated substitution的變形，節點表示每個展開代入的cost

### 1. $T(n) = 4T(\frac{n}{2}) + n$, $T(1) = 1$ 


<img src="https://i.imgur.com/umv7n9P.png" width="80%"/>

每個$T(\frac{n}{2})$再遞迴地代入

<img src="https://i.imgur.com/3290bSI.png" width="80%"/>

最終會迭代到$T(1) = 1$，長成高度$log_{2}n$的遞迴樹

<img src="https://i.imgur.com/Zrq15vT.png" width="98%"/>

$= \left [n + 2n+ \dots +2^{(log_{2}n - 1)}n \right ] + 4^{log_{2}n}T(1)$


$= (2^{log_{2}n} -1)n + n^{2}$

$= n^{2} - n + n^{2}$

$= 2n^{2}-n \in O(n^{2})$

### 2. $T(n) = T(\frac{2}{3}n) + T(\frac{1}{3}n) + n$, $\forall n \geq 2$, $T(1)=1$


![](https://i.imgur.com/peyUiJn.png)

每層cost皆為$n$，完整層(較矮那邊)高度為$log_{3}n$
若整棵樹為full complete，高度為$log_{1.5}n$，實際的cost會被bounded在這之間
$nlog_{3}n \leq nlog_{2}n \leq nlog_{1.5}n$

$\therefore T(n) = \Theta (nlog_{2}n)$



# Master Theorem

每次用recursion tree都要做等比級數、加法等運算，其實可以歸納出一套定理來快速地看出general case的遞迴時間函數

若遞迴時間函數符合以下形式:

$$
T(n) = aT(\frac{n}{b}) + f(n)
$$

$$
a \geq 1, b \geq 1, f(n) \text{ is asymptotically positive}, \forall n \text{ sufficiently large}
$$

* **case 1**: $f(n) = O(n^{log_{b}a} \cdot n^{-\varepsilon })$ where $\varepsilon > 0$ $\Rightarrow T(n) = \Theta (n^{log_{b}a})$
* **case 2**: $f(n) = \Theta(n^{log_{b}a}  \cdot log^{k}n)$ where $k \geq 0 \Rightarrow T(n) = \Theta (f(n)log_{2}n)$
* **case 3**: $f(n) = \Omega (n^{log_{b}a} \cdot n^{\varepsilon})$ where $\varepsilon > 0$ and $af(\frac{n}{b}) \leq cf(n)$, for some $c < 1 \space \forall n \Rightarrow T(n) = \Theta (f(n))$


## Examples

#### 1. $T(n) = 4T(\frac{n}{2}) + n$

$a=4, b=2$

$f(n) = n = O(n^{2} \cdot n^{- \varepsilon})$ where $0 < \varepsilon < 1$

Apply master theorem **case 1**

$\Rightarrow T(n) = \Theta (n^{2})$

Q: 為什麼master theorem不需要初始條件? $T(1) = 1$, $T(1) = 10$, $T(1) = 100$有什麼差別?
A: 初始條件只會影響$n^{2}$的係數

#### 2. $T(n) = 3T(\frac{n}{2}) + 8n$

$a=3, b=2$

$n^{log_{b}a} = n^{log_{2}3} = n^{1.585...}$

$f(n) = 8n = O(n^{log_{2}3} \cdot n^{-\varepsilon})$ where $0 < \varepsilon < 0.5$

Apply master theorem **case 3**

$\Rightarrow T(n) =\Theta (n^{log_{2}3})$


#### 3. $T(n) = 4T(\frac{n}{2}) + n^{2}log^{10}n$

$a=4,b=2$, $n^{log_{b}a} = n^{2}$

$f(n)=n^{2}log^{10}n = \Theta (n^{log_{b}a} \cdot log^{k}n)$, $k=10$

Apply master theorem **case 2**

$\Rightarrow T(n) = \Theta (n^{2}log^{11}n)$

#### 4. $T(n) = 4T(\frac{n}{2}) + n^{3}$

$a=4, b=2$, $n^{log_{b}a}= n^{2}$

$f(n) = n^{3}= \Omega (n^{2} \cdot n^{\varepsilon})$, where $0 < \varepsilon \leq 1$


Apply master theorem **case 3**

$\Rightarrow T(n) = \Theta (n^{3})$

#### 5. $T(n) = 2T(\sqrt{n}) + log_{2}n$, $T(2)=0$

這題較特殊，要先變數變換

let $m = log_{2}n \Rightarrow n = 2^{m}$

$T(2^{m}) = 2T(2^{\frac{m}{2}}) + m$, $T(2)= 0$

let $S(m) = T(2^{m})$

$S(m) = 2S(\frac{m}{2}) + m$, $S(1)=0$

$= mlog_{2}m$, by master theorem **case 2**

$T(n) = T(2^{m}) = S(m)$

$= mlog_{2}m$

$= log_{2}n \cdot log_{2}log_{2}n$

## Prove master theorem case 3

$T(n) = aT(\frac{n}{b}) + f(n)$

$= a \left [aT(\frac{n}{b^{2}}) + f(\frac{n}{b}) \right ] + f(n)$

$= a^{2}T(\frac{n}{b^{2}}) + af(\frac{n}{b})+f(n)$

$= \dots$

$= a^{i}T(\frac{n}{b^{i}}) + \left [ a^{i-1}f(\frac{n}{b^{i-1}}) + \dots +a^{2} f(\frac{n}{b^{2}}) + af(\frac{n}{b}) +f(n) \right ]$

$n=b^{i}$, $\therefore$做$i$次, $i=log_{b}n$時迭代完畢

$= a^{log_{b}n}T(1) + \left [ a^{i-1}f(\frac{n}{b^{i-1}}) + \dots +a^{2} f(\frac{n}{b^{2}}) + af(\frac{n}{b}) +f(n) \right ]$

換底公式

$= n^{log_{b}a}T(1) + \left [ a^{i-1}f(\frac{n}{b^{i-1}}) + \dots +a^{2} f(\frac{n}{b^{2}}) + af(\frac{n}{b}) +f(n) \right ]$


上式主要分2項，哪邊較大會決定哪個case

$\left [ a^{i-1}f(\frac{n}{b^{i-1}}) + \dots +a^{2} f(\frac{n}{b^{2}}) + af(\frac{n}{b}) +f(n) \right ]$裡面最大的一項為$f(n)$

$\therefore af(\frac{n}{b}) \leq c \cdot f(n)$, $c < 1$會成立，依此類推可得$a^{2}f(\frac{n}{b^{2}}) \leq c \cdot af(\frac{n}{b}) \leq c^{2}f(n)$

$\Rightarrow \left [ a^{i-1}f(\frac{n}{b^{i-1}}) + \dots +a^{2} f(\frac{n}{b^{2}}) + af(\frac{n}{b}) +f(n) \right ] \leq \left [ \dots + c^{2}f(n) + cf(n) + f(n) \right ]$

因此$T(n)$可寫成

$T(n) = n^{log_{b}a} \cdot T(1) + \left [ \dots +a^{2} f(\frac{n}{b^{2}}) + af(\frac{n}{b}) +f(n)  \right ]$

$\leq n^{log_{b}a} \cdot T(1) + \left [ \dots + c^{2}f(n) + cf(n) + f(n) \right ]$


$T(n) \leq n^{log_{b}a}T(1) + \frac{f(n)}{1-c} \in \Theta (f(n))$


