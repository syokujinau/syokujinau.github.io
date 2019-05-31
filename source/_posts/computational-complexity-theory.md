---
title: 計算複雜度理論簡介
date: 2019-05-31 16:25:30
tags:
- Algorithm
thumbnail: https://i.imgur.com/nzJdLTR.jpg
---

主要介紹
* P
* NP
* NP-Complete

## Decision Problem


一個計算問題，輸出只有True/False的某種演算法
例如:

* 給定一個圖$G$，是否連通?

shortest problem也可以改寫成Decision Problem
* 給定一個圖$G$，兩點$u、v$，且它們的cost為$k$，是否存在一個path，其cost小於$k$?

經典的計算難題: Hamiltonian path、cycle - pass all vertices exactly once
* 給定一個圖$G$與起點$s$是否存在一個path、cycle，能夠剛好經過所有頂點一次?

<img src="https://i.imgur.com/niyYfHS.png" width="550px">


## $P$ Problems 

一個Decision Problem屬於$P$，若且唯若存在一個poly-time演算法$A(s)$使得
* 一個instance $s$是true$\Leftrightarrow A(s) = 1$
* 一個instance $s$是false$\Leftrightarrow A(s) = 0$

![](https://i.imgur.com/3vDeqjn.png)

<!-- more -->

> 所謂instance可以當作演算法的input，例如Hamiltonian path的instance就是一個graph

## $NP$ Problems

Nondeterministic Polynomial Time 
一個Decision Problem屬於$NP$，若且唯若存在一個執行在$poly(\left | s \right |)$時間的演算法$B(s, t)$，使得
* 一個instance $s$是true$\Leftrightarrow \exists t, B(s, t) = 1$
* 一個instance $s$是false$\Leftrightarrow \forall t, B(s, t) = 0$

![](https://i.imgur.com/qZJztd4.png)


> 1. $\left | s \right |$代表輸入instance的個數
> 2. $t$是一個random bit可想成丟銅板，若input是true，**至少存在一個**演算法的output是true，但input是false則**所有**output皆須是0
>     * 答案是true，存在某個$t$使答對機率$> 0$
>     * 答案是false，對所有$t$使答對機率$=100\%$


### Thm: INDEPENDENT SET $\in$ $NP$

給定無向圖$G=(V,E)$與常數$k$，$G$中是否有一個size為$k$的independent set?

> independent set: 一組兩兩不相鄰的頂點集合


**pf:** 
* Construct algo $B(s,t)$
* 給定任何instance $s$，也就是$G=(V,E)$，constant $k$
* $B(s,t)$
    1. If $\left | t \right | \neq \left | v \right |$
        * True $\Rightarrow$ 0
        * False $\Rightarrow$ 1
    2. If 「# of 1 in $t$」 $\neq k$
        * True $\Rightarrow$ 0
        * False $\Rightarrow$ 1
    3. If $t[i] = t[k] = 1$, but $(i,j) \in E$
        * True $\Rightarrow$ 0
        * False $\Rightarrow$ 1



### Thm: $X \in P \Rightarrow X \in NP$

**pf:** 

$X \in P \Rightarrow \exists$ poly-time Algo. $A(s)$

Let $B(s,t) = A(s)$ $\forall t$
$\Rightarrow X \in NP$



## $NP-complete$ Problems 


一個Decision Problem $X$屬於$NP$，若且唯若
1. $X \in NP$
2. for all $Y \in NP$，$Y$ is poly-time reducible(改寫) to $X$

> reducible意思是能mapping成另一個**更難的**問題，使得輸入與對應的輸出相同

### Thm: HAM. PATH is poly-time reducible to HAM. CYCLE

**pf:**

<img src="https://i.imgur.com/dXgSziL.png" width="250px">

<br/><br/>

把HAM. PATH的instance $G$ reduce為$G^{'}$，方法是在原有的vertex各拉一條edge到一個新的vertex

![](https://i.imgur.com/JEtHQkC.png)




### Thm: HAM. PATH $\in$ $NP-complete$, then the HAM. CYCLE  $\in$ $NP-complete$

**pf:** 

若從某個已知是$NP-complete$問題$Y$可以poly-time reduce到HAM. PATH，則也能間接證明HAM. CYCLE也是$NP-complete$

![](https://i.imgur.com/9RxqEZS.png)



## 證明某問題為$NP-complete$的方法

$X$: new problem
$Y$: old problem，已知為$NP−complete$

如果符合以下三個條件
1. $X \in NP$
2. $Y \in NP-complete$
3. $Y$ is poly-time reducible to $X$

則可證明

Problem $X \in NP-complete$

### Fisrt $NP-complete$ problem

由Cook Levin於1971年證明The Satisfiability Problem (SAT)屬於$NP-complete$，可以由這個問題當作$Y$ problem，並於poly-time reduce成某個新的$X$ problem，使這兩種演算法輸出皆相同，即證明$X$ problem也是$NP-complete$

SAT問題如下(舉例):

> Given a logic statement of the form $(x_1 + x_2 + \overline{x_3}) \cdot (x_2 + x_4) \cdot (\overline{x_2} + x_4)$, can we assign each $x_i$ to true/false s.t. the statement is true? 
>
> 註: 每個括號稱為clause，如果一個clause包含3個以下的變數，則此問題稱為$3SAT$

### 證明 INPED. SET $\in NP-complete$

INPED. SET敘述:

> Given a graph $G = (V,E)$ and a constant $k$, are there $k$ vertices in $G$ s.t. no edges between them.

1. $X \in NP$
    * INDEP. SET $\in NP$
3. $Y \in NP-complete$
    * SAT $\in NP-complete$
5. $Y$ is poly-time reducible to $X$
    * Reduction:
        * 對每個變數$x_i$、$\overline{x_i}$皆用頂點表示
        * 同一個clause中的變數彼此用一條邊連接
        * 對$x_i$、$\overline{x_i}$之間用一條邊連接
        * 令$k$為clause的個數
        * ![](https://i.imgur.com/sdo3oe1.png)
            * 將$x_1 = 1、x_2=1、x_4 = 1$代回$(x_1 + x_2 + \overline{x_3}) \cdot (x_2 + x_4) \cdot (\overline{x_2} + x_4)$輸出也是True
            * INPED. SET $\in NP-complete$得證

 
### 證明 VERTEX COVER $\in NP-complete$

> A vertex cover $S$ is a set of vertices s.t. $\forall (u,v) \in E$ either $u \in S$ or $v \in S$
> 
> Given  a graph $H$, constant $l$, does $H$ have a vertex cover of size $l$?

e.g. 

$H$:
<img src="https://i.imgur.com/BWcuAIE.png" width="200px">

{% raw %}
$S = \left \{ b,c,d,f,g \right \}, l = 5$
{% endraw %}

以下為證明流程:

1. $X \in NP$
    * VERTEX COVER $\in NP$ (Trivial)
2. $Y \in NP-complete$
    * INDEP. SET $\in NP-complete$
3. $Y$ is poly-time reducible to $X$
    * Reduction $f$:
    * $f:$ 
        * $H=G$
        * $l = \left | V \right | - k$
        * Notice that $f$ should be poly-time in "size of $G,k$"
          ![](https://i.imgur.com/wsCaLrI.png)

每個邊至少會有一個edge在VERTEX COVER Set之中，暗示這個Set之外的vertex pair皆不相鄰

<img src="https://i.imgur.com/IHwcEIL.png" width="300px">


### 證明 SET COVER $\in NP-complete$

> Given  a set {% raw %}$U =  \{ x_1,x_2,\dots,x_m  \}${% endraw %}
> $n$ subsets $S_1、S_2、\dots、S_n \subseteq U$
> constant $k$
> Are there $k$ subsets such that the union of these $k$ subsets is $U$?

e.g.

{% raw %}
$U= \{x_1, x_2, x_3, x_4 \} \\$

$n=3$ subsets 

$S_1 =  \{x_1, x_2 \} \\ S_2 =  \{ x_2,x_3,x_4 \} \\ S_3 =  \{ x_3,x_4  \}$
{% endraw %}

$k=2$，是否有2個subsets的union會是$U$?

1. $X \in NP$
    * SET COVER $\in NP$ (Trivial)
2. $Y \in NP-complete$
    * VERTEX COVER $\in NP-complete$
3. $Y$ is poly-time reducible to $X$
    * Reduction $f$:
    * $f:$ 
        * $U=E$
        * {% raw %}$S_i = \{ e | e \text{ is connected to }v_i \}${% endraw %}        
        * $k=l$


e.g.

<img src="https://i.imgur.com/v7rubuh.png" width="300px">


{% raw %}
$U =  \{ x_1, x_2, x_3, x_4, x_5  \} \\ S_1 =  \{ x_1, x_2   \} \\ S_2 =  \{ x_1 , x_2  \} \\ S_3 =  \{ x_2 , x_3 , x_4   \} \\ S_4 =  \{ x_5   \} \\ S_5 =  \{ x_4 , x_5   \}$
{% endraw %}

Are there $k$ subsets such that the union of these $k$ subsets is $U$?

<!-- 註記:
有用 \left \{ \right \} 的LaTeX語法
{% raw %}
{% endraw %} -->