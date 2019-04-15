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

{% raw %}
$$
P(i, j) = \begin{cases}
1 & \text{ if } i=0 \\ 
1 & \text{ if } j=0 \\ 
P(i-1, j) + P(i, j-1) & \text{ if } i \geq 1, j \geq 1 
\end{cases}
$$
{% endraw %}

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
* 時間複雜度: $O(mn)$



---


## Max subsequence sum

給予$\left [ a_{1}, a_{2}, \dots , a_{n} \right ]$
 
求連續子序列之和的最大值$Max_{i,j} = a_i +a_{i+1} + \dots + a_{j}$

**Subproblem:**
* $R_i$為$\left [ a_1, a_2, \dots, a_i \right ]$的最大子序列之和
* $M_i$代表目前的子序列和最大值

**Init:**
$R_1 = a_1$
$M_1 = a_1$


{% raw %}
$$
R_i = max \begin{cases}
R_{i-1} + a_i &  \\ 
a_i &  
\end{cases}
$$


$$
M_i = max \begin{cases}
M_{i-1} &  \\ 
R_i &  
\end{cases}
$$
{% endraw %}


[53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)
 
```cpp
int maxSubArray(vector<int>& nums) {
    const int len = nums.size();
    vector<int> M(len); // max subseq so far
    vector<int> R(len); // R[i] means max subseq (contain ai)

    // init
    M[0] = R[0] = nums[0];

    // update
    for(int i = 1; i < len; ++i) {
        R[i] = max(R[i - 1] + nums[i], nums[i]);
        M[i] = max(M[i - 1], R[i]);
    }

    return M[len - 1];
}
```

* 時間複雜度: $O(n)$


---



## Optimal Investment

&emsp; Suppose there are $m$ different money investment plans $P_1, P_2,..., P_m$ available, Plan $P_i$ takes $y_i$ years and will increase your money by a factor of $(1+r_i)^{y_i}$. (In other words, if you invest $D$ dollars in plan $P_i$, after $y_i$ years, you will have $D(1+r_i)^{y_i}$ dollars.) For every $i$, $y_i$ is a positive integer and $r_i \geq 0$.

&emsp; You start with some fixed amount of money $M$ and need to choose a series of plans to invest one by one. You must invest all your money into a single plan at any given time. You must select some plans with the total duration at most $n$ years. The goal is to maximize your money at the end of these investments. The series of plans you choose may include the same plan any number of times. Assume that the annual interest rate $r_i$ satisfies $r_1 \geq r_2 \geq ... \geq r_m$

1. Design an $O(mn)$-time algorithm which finds the final amount of money after the optimal series of investment. 

共$n$年，有$m$個plan可選擇

**Subproblem:**

令$C(i, j)$為max profit，代表只用了$P_1, P_2,...,P_i$的投資組合於$j$年之間


{% raw %}
$C(i,j) = max \begin{cases}
C(i-1 , j) & \text{, don't invest } P_i \\
C(i, j-y_i) \cdot (1+r_i)^{y_i} & \text{, invest } P_i \text{ at least once} 
\end{cases}$
{% endraw %}

**Algo:**

> {% raw %}$\text{for }i = 1 \text{ to }m \\ \space \space \space \space \text{for }j = 1 \text{ to } n \\ \space \space \space \space \space \space \space \space C(i,j) = max \begin{cases} C(i-1 , j) & \text{, don't invest } P_i \\ C(i, j-y_i) \cdot (1+r_i)^{y_i} & \text{, invest } P_i \text{ at least once} \end{cases} \\ \text{output: }C(m,n)${% endraw %}





2. Adding an extra restriction that each plan can only be invested **at most twice**, Also, you are limited to choosing series of **at most $t$ plans** (Investing in the same plan twice counts as two). *Design a polynomial time algorithm which finds the optimal set of plans.* Any algorithm which runs in time polynomial in $mnt$ suffices.

**Subproblem:**

令$C(i,j,k)$為max profit，代表
* 使用$P_1, P_2, \dots, P_i$
* 投資$\leq j$年
* 共使用了$\leq k$個plan

{% raw %}
$C(i,j,k) = max \begin{cases} C(i-1 , j,k) & \text{, don't invest } P_i \\ C(i-1, j-y_i, k-1) \cdot (1+r_i)^{y_i} & \text{, invest } P_i \text{ once} \\ C(i-1, j-2y_i, k-2) \cdot (1+r_i)^{2y_i} & \text{, invest } P_i \text{ twice} \end{cases}$
{% endraw %}

**Algo:**

> {% raw %}$\text{for }i = 1 \text{ to }m \\ \space \space \space \space \text{for }j = 1 \text{ to } n \\
\space \space \space \space \space \space \space \space  \text{for }k = 1 \text{ to }t \\ \space \space \space \space \space \space \space \space \space \space \space \space C(i,j,k) = max \begin{cases} C(i-1 , j,k) & \text{, don't invest } P_i \\ C(i-1, j-y_i, k-1) \cdot (1+r_i)^{y_i} & \text{, invest } P_i \text{ once} \\ C(i-1, j-2y_i, k-2) \cdot (1+r_i)^{2y_i} & \text{, invest } P_i \text{ twice} \end{cases} \\ \text{output: }C(m,n,t)${% endraw %}



---


## Trip Planning


一個旅途$[0, 1, 2, ..., n]$，每1km有休息區

1. 起點為$0$
1. 停在第$i$點的住宿費為$C_i$
2. 每天只走20km以內

想求得

1. $0$走到$n$的最小花費
2. 整個旅途中住過哪些休息區

### Solution

* 最後停在$n$，所以必要支出$C_n$
* Subproblem: $A[i]$表示可走到$i$的最小花費
* 紀錄路徑於$B_i$，表示到達$A_i$之前的位置(第$i$公里)

**init:**

前1~20公里最省錢方法就是直接一開始直接到達，B則全部初始化為0

$A[1,2, \dots ,20] = C[1,2, \dots ,20]$

$B[i] = 0 \text{ , where } i = 0 \sim n$

**update:**

$A \left [ i \right ] = min(A \left [ i - 20 \right ], A \left [ i - 19 \right ],...,A \left [ i - 1 \right ]) + C_i$

$B \left [i \right ] = j \text{ , if the above min pick } A \left [ j \right ]$ 



---

## Resturant DP

* Groups: $G_1, G_2, \dots , G_m$
* number of people: $a_1, a_2, \dots , a_m$
* dollars per person: $d_1, d_2, \dots , d_m$

Want: $Max \sum_{S} a_{i}d_{i}$ , subject to $\sum_{S} a_{i} \leq n$

**Subproblem:**


* {% raw %}$S(j,k)$ : $Max \sum_{S} a_{i}d_{i}$, subject to $\begin{cases} \sum_{S}a_{i} & \\ S \subseteq \left \{ G_1, G_2, \dots, G_{j} \right \} & \end{cases}${% endraw %}
* {% raw %}$T(j,k)$ : set of groups selected in $S(j,k)${% endraw %}

{% raw %}
$S(j,k) = max \begin{cases} S(j-1,k- a_j) + a_{j}d_{j} & (\text{ accept }G_{j}) \\ S(j-1, k) & (\text{ don't accept }G_{j}) \end{cases}$

$T(j,k) = \begin{cases} 0 & \text{ if } T(j-1,k-a_{j}) \cup \left \{ G_{j} \right \} (\text{ accept }G_{j}) \\ 1 & \text{ if } S(j-1, k) (\text{ don't accept }G_{j}) \end{cases}$
{% endraw %}

**Algo:**

> {% raw %}$\text{for } j = 1 \text{ to } m \\ \space \space \space \space \text{for }k = 1 \text{ to }n \\ \space \space \space \space \space \space \space \space S(j,k) = max \begin{cases} S(j-1,k- a_j) + a_{j}d_{j} & (\text{ accept }G_{j}) \\ S(j-1, k) & (\text{ don't accept }G_{j}) \end{cases} \\ \space \space \space \space \space \space \space \space T(j,k) = \begin{cases} 0 & \text{ if } T(j-1,k-a_{j}) \cup \left \{ G_{j} \right \} (\text{ accept }G_{j}) \\  1 & \text{ if } S(j-1, k) (\text{ don't accept }G_{j}) \end{cases} \\ \text{output: }S(m,n), T(m,n)${% endraw %}


* **Time Complexity:** $O(mn)$
* **Space:** 
    * $S(j,k)$: $\Theta (mn)$
    * $T(j,k)$: $\Theta (m^{2}n)$



---

## Sequence Alignment

Input: 
* X: $ACAAT$
* Y: $AGATG$

Let mismatch costs $C_1$, gap costs $C_2$

Output: min cost

ex.

1. 
    $ACAAT$
    $AGATG$ $\Rightarrow$ 3 mismatch
    

2.  
    $ACAAT\text{ _ }$
    $AGA\text{ _ }TG$ $\Rightarrow$ 1 mismatch, 2 gaps

3.
    $ACAAT\text{ _ _ _ _ }$
    $A\text{ _ _ _ _ }GATG$ $\Rightarrow$ 8 gaps

**Subproblem:**

$C(i, j)$代表min cost of matching "first $i$ symbols of $X$" and "first $j$ symbols of $Y$"

ex.
$C(3,3)$: min cost of matching $ACA$ and $AGA$
$C(1,5)$: min cost of matching $A$ and $ACTAG$


* case1: 

######################


