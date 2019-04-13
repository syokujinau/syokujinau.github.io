---
title: Divide and Conquer
date: 2019-03-13 15:24:45
tags:
- Algorithm
thumbnail: https://i.imgur.com/q4Mqv51.jpg
---


D&C演算法大致會是以下流程

1. Split into subproblems
2. Solve subproblems **recursively**
3. Merge solutions


## Find MaxMin

給一序列$data = \left [ a_1, a_2, \dots , a_n \right ]$
只能用pairwise comparison，求這個序列的最大與最小值

* Brute force

(n-1)次比較找到MAX，(n-2)次比較找到min，共需要$2n - 3$時間

* D&C

```c 
MaxMin(data, 1, n) {
    [Max1, min1] = MaxMin(data, 1, n/2)
    [Max2, min2] = MaxMin(data, n/2 + 1, n)
    return [max(Max1, Max2), min(min1, min2)]
}
```

$T(n) = 2T(\frac{n}{2}) + 2$, 若只有2數只需1次比較$T(2) = 1$

$= \frac{3}{2}n - 2$

## Max Subsequence Sum
<!-- more -->
給一序列，$data = \left [ a_1, a_2, \dots, a_n \right ]$

求最大的連續子序列之和，$Max_{i,j} = a_i + a_{i+1} + \dots + a_{j}$ 

ex. 

$\left [ -5, 12, -3, -4, 15, -6\right ]$ 其中最大子序列$\left [ 12, -3, -4, 15\right ]$，總和為20

$\left [ -5, 12, -10, -11, 15, -6\right ]$ 其中最大子序列$\left [ 15\right ]$，總和為15

* Brute force

約$\frac{n^{2}}{2}$種不同的子序列，每個序列最多$n$筆資料
$\Rightarrow \frac{n^{3}}{2} \in O(n^{3})$

* D&C

```c
MaxSubseqSum(data, 1, n) { // 從1~n中，求最大子序列之和
    if n = 1, return a1
    
    m1 = MaxSubseqSum(data, 1, n/2)
    m2 = MaxSubseqSum(data, n/2 + 1, n)
    m3 = max_i(data, 1, n/2) // 從n/2往前加i個的最大值 O(n)
    m4 = max_j(data, n/2 + 1, n) // 從n/2 + 1往後加j個的最大值 O(n)
    return max(m1, m2, m3+m4)
}
```

$T(n) = 2T(\frac{n}{2}) + \Theta (n)$

$= \Theta (nlogn)$

