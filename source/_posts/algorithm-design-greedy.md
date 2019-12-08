---
title: 演算法設計 - Greedy Algorithm
date: 2019-04-14 17:43:51
tags:
- Algorithm
categories:
- Algorithm
thumbnail: https://i.imgur.com/sUq9XBU.jpg
---


Greedy演算法的設計精神

1. Build a solution in small steps
2. At each step optimize locally


## Meeting Scheduling

給予$M_1, M_2, \dots, M_n$個會議
$M_i$的開會時間從$s_i$到$t_i$
求最多可參加幾場會議

Greedy想要**盡可能**的每一步都取目前最佳解法，並且嘗試證明沒有比這個方法更好的解法，**期待**而**不保證**會得到整體最佳解。

以下列出一些可能的greedy解法

$greedy1$. 選離目前$t_i - s_i$最短的
```
greedy :            |------|
optimal: |-------------| |------------|
```

$greedy2$. 選離目前最少衝突的會議

```
            |----|        |----|
            |----|        |----|
greedy :           |----|
optimal: |----||-----| |----| |----|
```
<!-- more -->
$greedy3$. 選離目前最早開始的，即$s_i$最小的

```
greedy : |-----------------------------|
optimal:    |----|    |----|   |----|
```

$greedy4$. 選離目前最早結束的，即$t_i$最小的


假設存在某個演算法opt的會議數比$greedy4$還多1個
* Greedy: $M_1, M_2, M_3, \dots, M_k$
* opt: $M_{1}^{'}, M_{2}^{'}, M_{3}^{'}, \dots, M_{k}^{'}, M_{k+1}^{'}$

若證明opt有矛盾，就能確定$greedy4$演算法為optimal

上面的取法imply
* $t_{1} \leq t_{1}^{'}$
* $t_{k}^{'} < t_{k}$

驗證上式是否可能存在$j$，使得
* $t_{j} \leq t_{j}^{'}$
* $t_{j+1} > t_{j+1}^{'}$

```
             M_{j}         M_{j+1}
Greedy: |------------|  |------------| 
             M'_{j}        M'_{j+1}
opt   :   |-----------|   |--------|
```

此種情況有矛盾，因為$greedy4$應該會選擇$M_j、M_{j+1}^{'}$，因此證明$greedy4$演算法本身即為optimal





---


## Restaurant (Greedy)

* Groups: $G_1, G_2, \dots , G_m$
* number of people: $a_1, a_2, \dots , a_m$
* dollars per person: $d_1, d_2, \dots , d_m$
(Assume $d_1 \geq d_2 \geq \dots \geq d_m$)

Want: $Max \sum_{S} a_{i}d_{i}$ , subject to $\sum_{S} a_{i} \leq n$

Greedy algorithm pick $G_1, G_2, \dots, G_k$ until $G_{k+1}$ doesn't fit $\sum_{S} a_{i} \leq n$ (加入$a_{k+1}$座位數剛好不夠)

可能的greedy策略:

1. 因為$d_1 \geq d_2 \geq \dots \geq d_k \geq d_{k+1}$，所以選$G_1, G_2, \dots , G_k$
counter example: $m = 2$
    * $a_1 = 1, d_1 = 100$ 
    * $a_2 = n, d_2 = 99$
    * greedy profit = 100 dollars, opt profit = 99n (better)

2. Pick $max \begin{cases} G_1, G_2, \dots , G_k & \newline G_{k+1} & \end{cases}$



