---
title: Leetcode - Combinations
date: 2019-12-07 14:11:46
tags:
- Algorithm
- Backtracking
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/Vho7yDR.png
---

## [77. Combinations](https://leetcode.com/problems/combinations/)

Given two integers n and k, return all possible combinations of k numbers out of 1 ... n.

Example:

```
Input: n = 4, k = 2
Output:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

<!-- more -->



## Permutation與Combination的性質與基本DFS架構

> 都需要一個d參數代表遞迴深度，cur為目前的node狀態，一開始的if檢查是否到達leaf，是則紀錄答案，for代表該node的子樹，畫出tree遞迴過程就能理解(只需畫出部分，從root到leaf即可)，針對題目變化也較容易想到剪枝的方法






||Permutation|Combination|
|---|---|---|
||n物取r個的直線排列數: $P^{n}_{r} = \frac{n!}{(n-r)!}$|n物取r個的組合數: $C^{n}_{r} = \frac{n!}{r!(n-r)!}$|
||需要一個used陣列紀錄用過的元素| 需要s紀錄start|
| pseudocode |<img src="https://i.imgur.com/G4zORVo.png"  />|<img src="https://i.imgur.com/wnU6FHB.png"  />|

## Solution

$C^{3}_{2} = \frac{3!}{2!1!} = 3$種不同組合

<img src="https://i.imgur.com/qBIvlT5.png" width="50%" />

```cpp
class Solution {
public:
    vector<vector<int>> combine(int n, int k) {
        
        vector<vector<int>> ans;
        vector<int> cur;
        
        function<void(int, int)> dfs = [&](int d, int s) {
            if(d == k) {
                ans.push_back(cur);      
                return;
            }
            
            for(int i = s; i <= n; ++i) {
                cur.push_back(i);
                dfs(d + 1, i + 1);
                cur.pop_back();
            }
        };
        
        dfs(0, 1);
        
        return ans;
    }
};
```