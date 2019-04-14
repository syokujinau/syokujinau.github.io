---
title: 演算法設計 - Dynamic Programming
date: 2019-04-14 17:44:07
tags:
- Algorithm
thumbnail: https://i.imgur.com/RPQGyZx.jpg
---
 

基本設計概念:

* Solve a **pre-determined** set of problems
* Memorize solutions to all subproblems


## Unique Path


走到(i, j)的位置，只有可能來自上面或左邊
令$P(i, j)$表示走到(i, j)位置的方法數

$$
P(i, j) = \begin{cases}
1 & \text{ if } i=0 \\ 
1 & \text{ if } j=0 \\ 
P(i-1, j) + P(i, j-1) & \text{ if } i \geq 1, j \geq 1 
\end{cases}
$$

又稱為state equation

[62. Unique Paths](https://leetcode.com/problems/unique-paths/)
<!-- more -->
```cpp
int uniquePaths(int m, int n) {
    // create a m*n matrix
    vector<vector<int>> P(m);
    for(int i = 0; i < m; ++i) P[i].resize(n);

    // init
    for(int j = 0; j < n; ++j) 
        P[0][j] = 1;
    
    for(int i = 0; i < m; ++i) 
        P[i][0] = 1;
    

    // update
    for(int i = 1; i < m; ++i) 
    	for(int j = 1; j < n; ++j) 
            P[i][j] = P[i - 1][j] + P[i][j - 1];

 
    return P[m - 1][n - 1];
}
```
時間複雜度: $O(mn)$


## Optimal Investment

&emsp; Suppose there are $m$ different money investment plans $P_1, P_2,..., P_m$ available, Plan $P_i$ takes $y_i$ years and will increase your money by a factor of $(1+r_i)^{y_i}$. (In other words, if you invest $D$ dollars in plan $P_i$, after $y_i$ years, you will have $D(1+r_i)^{y_i}$ dollars.) For every $i$, $y_i$ is a positive integer and $r_i \geq 0$.

&emsp; You start with some fixed amount of money $M$ and need to choose a series of plans to invest one by one. You must invest all your money into a single plan at any given time. You must select some plans with the total duration at most $n$ years. The goal is to maximize your money at the end of these investments. The series of plans you choose may include the same plan any number of times. Assume that the annual interest rate $r_i$ satisfies $r_1 \geq r_2 \geq ... \geq r_m$

1. Design an $O(mn)$-time algorithm which finds the final amount of money after the optimal series of investment. 

共$n$年，有$m$個plan可選擇
令$C(i, j)$為max profit，代表只用了$P_1, P_2,...,P_i$的投資組合於$j$年之間


Subproblem: 
$$
C(i,j) = max \begin{cases}
C(i-1 , j) & \text{, don't invest } P_i \\
C(i, j-y_i) \cdot (1+r_i)^{y_i} & \text{, invest } P_i \text{ at least once} 
\end{cases}
$$

S的意義 ?? 如何更新 ??

$S(i, j) = 1$表示$j$年時有投資$P_i$ (??)



2. Adding an extra restriction that each plan can only be invested **at most twice**, Also, you are limited to choosing series of at most $t$ plans (Investing in the same plan twice counts as two). Design a polynomial time algorithm which finds the optimal set of plans. Any algorithm which runs in time polynomial in $mnt$ suffices.





## Trip Planning


一個旅途$[0, 1, 2, ..., n]$，每1km有休息區

1. 起點為$0$
1. 停在第$i$點的住宿費為$C_i$
2. 每天只走20km以內

想求得

1. $0$走到$n$的最小花費
2. 整個旅途中住過哪些休息區

### Soltion

* 最後停在$n$，所以必要支出$C_n$
* Subproblem: $A[i]$表示可走到$i$的最小花費
* 紀錄路徑於$B_i$，表示到達$A_i$之前的位置(第$i$公里)

**init:**

前1~20公里最省錢方法就是直接一開始直接到達，B則全部初始化為0

$$
A[1 ... 20] = C[1 ... 20]  \\
B[i] = \left \{0 \right \}
$$

**update:**

$$
A[i] = min(A[i-20], A[i-19],...,A[i-1]) + C_i \\
B[i] = j \text{ , if the above min pick A[j]}
$$


