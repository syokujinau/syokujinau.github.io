---
title: Leetcode - Permutations
date: 2019-12-07 14:31:42
tags:
- Algorithm
- Backtracking
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/DLgkt0w.png
---

## [46. Permutations](https://leetcode.com/problems/permutations/)

Given a collection of distinct integers, return all possible permutations.

Example:

```
Input: [1,2,3]
Output:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

<!-- more -->



## Permutation與Combination的性質與基本DFS架構

> 都需要一個d參數代表遞迴深度，cur為目前的node狀態，一開始的if檢查是否到達leaf，是則紀錄答案，for代表該node的子樹，畫出tree遞迴過程就能理解(只需畫出部分，從root到leaf即可)，針對題目變化也較容易想到剪枝的方法



||Permutation|Combination|
|---|---|---|
||n物取r個的直線排列數: $P^{n}_{r} = \frac{n!}{(n-r)!}$|n物取r個的組合數: $C^{n}_{r} = \frac{n!}{r!(n-r)!}$|
||需要一個used陣列紀錄用過的元素| 需要s紀錄start|
| pseudocode |<img src="https://syokujinau.github.io/myimages/perm1.png"  />|<img src="https://syokujinau.github.io/myimages/perm2.png"  />|

### 其他變形

```
input:
[A, B, C, D, E]

output:
[A, B, C]
[A, B, D]
[A, B, E]
[B, C, D]
...
```

保持前後順序取3個直線排列，需紀錄start `s`



```cpp
const int n = nums.size();
vector<int> cur;
vector<bool> used(n, false);

function<void(int, int)> dfs = [&](int d, int s) {
    if(d == 3) {
        for(int el : cur) cout << el << " "; cout << endl;
        return;
    }
    
    for(int i = s; i < n; ++i) {
        if(used[i]) continue;
        used[i] = true;
        cur.push_back(nums[i]);
        dfs(d + 1, i + 1);
        cur.pop_back();
        used[i] = false;
    }
};
```

## Solution

套上面模板

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        
        vector<vector<int>> ans;
        vector<int> cur;
        vector<bool> used(nums.size(), false);
        
        function<void(int)> dfs = [&](int d) {
            if(d == nums.size()) {
                ans.push_back(cur);
                return;
            }
            
            for(int i = 0; i < nums.size(); ++i) {
                if(used[i]) continue;
                used[i] = true;
                cur.push_back(nums[i]);
                dfs(d + 1);
                cur.pop_back();
                used[i] = false;
            }
        };
        
        dfs(0);
        
        return ans;
    }
};
```