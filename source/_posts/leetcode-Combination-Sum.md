---
title: Leetcode - Combination Sum
date: 2019-12-30 14:11:46
tags:
- Algorithm
- Backtracking
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/ttDxLuM.png
---

## [39. Combination Sum](https://leetcode.com/problems/combination-sum/)

Given a set of candidate numbers (candidates) (without duplicates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.

The same repeated number may be chosen from candidates unlimited number of times.

Note:

* All numbers (including target) will be positive integers.
* The solution set must not contain duplicate combinations.

Example 1:

```
Input: candidates = [2,3,6,7], target = 7,
A solution set is:
[
  [7],
  [2,2,3]
]
```

<!-- more -->

## Solution

一開始先照combination DFS基本架構來產生各種組合

<img src="https://i.imgur.com/wnU6FHB.png" width="50%" />

sum參數以遞減方式查找和為target的路徑，這種方式跟[112. Path Sum](https://leetcode.com/problems/path-sum/description/)類似。

思考到達leaf的條件
1. sum == 0，找到解，記錄答案
2. sum < 0，不可能有解了，直接return (剪枝)


```cpp
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        
        vector<vector<int>> ans;
        vector<int> cur;
        
        function<void(int, int)> dfs = [&](int s, int sum) {
            if(sum == 0) {
                ans.push_back(cur);
                return;
            }
            
            if(sum < 0) return;
            
            for(int i = s; i < candidates.size(); ++i) {
                cur.push_back(candidates[i]);
                dfs(i, sum - candidates[i]);
                cur.pop_back();
            }
        };
        
        dfs(0, target);
                
        return ans;
        
    }
};
```