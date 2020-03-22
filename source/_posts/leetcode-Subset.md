---
title: Leetcode - Subset
date: 2020-02-13 14:21:16
tags:
- Algorithm
- Backtracking
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/4zvTNT9.jpg
---

## [78. Subsets](https://leetcode.com/problems/subsets/)

Given a set of distinct integers, nums, return all possible subsets (the power set).

Note: The solution set must not contain duplicate subsets.

Example:

```
Input: nums = [1,2,3]
Output:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

<!-- more -->

## Solution

DFS產生Combination

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> cur;
        
        function<void(int)> dfs = [&](int s) {
            ans.push_back(cur);
            for(int i = s; i < nums.size(); ++i) {
                cur.push_back(nums[i]);
                dfs(i + 1);
                cur.pop_back();
            } 
        };
        
        dfs(0);
        
        return ans;
    }
};
```