---
title: introduction-to-asymptotic-notation
date: 2019-02-28 12:05:54
tags: 
- Algorithm
- Time complexity
thumbnail: https://i.imgur.com/K6F6Ojx.jpg
---


# Introduction to Asymptotic Notation


> 衡量程式運行的時間量級，所關心的是輸入資料量成長的同時，演算法完成運算時間的成長率。


## Big-O: $O$


$f(n) = O(g(n))$ iff exists **two** positive constants $c$ and $n_{0}$ such that $f(n)\leq c\cdot g(n) \space \space \forall n \geq  n_{0}$ 



<img alt="bigO" src="https://i.imgur.com/9hS03jF.png" width="60%"/>

## Omega: $\Omega$

$f(n) = \Omega (g(n))$ iff exists **two** positive constants $c$ and $n_{0}$ such that $f(n) \geq c\cdot g(n) \space \space \forall n \geq n_{0}$
<!-- more -->
## Theta: $\Theta$

$f(n) = \Theta (g(n))$ iff exists **three** positive constants $c_{1}$、$c_{2}$ and $n_{0}$ such that $c_{1}\cdot g(n) \leq f(n) \leq c_{2}\cdot g(n) \space \space \forall n \geq n_{0}$

> 相對$O$與$\Omega$更嚴謹地表示演算法的時間複雜度

## little-o: $o$

$f(n) = o(g(n))$ iff exists **two** positive constants $c$ and $n_{0}$ such that $f(n)< c\cdot g(n) \space \space \forall n \geq  n_{0}$ 

## little-omega: $\omega$

$f(n) = \omega (g(n))$ iff exists **two** positive constants $c$ and $n_{0}$ such that $f(n) > c\cdot g(n) \space \space \forall n \geq n_{0}$


### Examples


#### Disprove that $\frac{1}{100} n\log_{2}{n} \in O(n)$ is false

$\frac{1}{100} n\log_{2}{n} \leq c \cdot n \space \space \forall n \geq n_{0}$
so $\frac{1}{100} \log_{2}{n} \leq c$
$\because$ c is not constant
$\therefore$ $\frac{1}{100} n\log_{2}{n} \notin O(n)$

#### If $g(n) \in O(f(n))$ and $g(n) \in \omega (h(n))$ then $f(n) \in \omega (h(n))$

$\because g(n) \in O(f(n))$
$\therefore g(n) \leq c_{1} \cdot f(n) \space \space \forall n \geq n_{0}$

$\because g(n) \in \omega (h(n))$
$\therefore g(n) > c_{2} \cdot h(n) \space \space \forall n \geq n_{0}$

$c_{2} \cdot h(n) < g(n) \leq c_{1} \cdot f(n)$
$\Rightarrow f(n) > c_{3} \cdot h(n) \space \space \forall n \geq n_{0}$ where $c_{3}$ and $n_{0}$ are positive constants
$\Rightarrow f(n) \in \omega (h(n))$ 