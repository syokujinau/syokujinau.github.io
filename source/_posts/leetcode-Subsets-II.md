---
title: Leetcode - Subset II
date: 2020-03-13 14:34:26
tags:
- Algorithm
- Backtracking
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/obvWQaH.jpg
---

## [90. Subsets II](https://leetcode.com/problems/subsets-ii/)

Given a collection of integers that might contain duplicates, nums, return all possible subsets (the power set).

Note: The solution set must not contain duplicate subsets.

Example:

```
Input: [1,2,2]
Output:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```

<!-- more -->

## Solution

和[Subsets](https://leetcode.com/problems/subsets/)差別是有重複元素，因此**排序**過讓相同元素相鄰，若`i != s`(非最左子樹)且跟前一個元素相同，代表這個子樹會產生同樣的combination，這種情況就直接跳過(剪枝)。

下圖edge上的數字是選擇的index `i`，紅字是`i == s`，灰色node為root的子樹將產生相同combination，因此不會拜訪(`continue`)。
<img src="https://i.imgur.com/vG7AI63.png" width="70%">

```cpp
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> cur;
        
        sort(nums.begin(), nums.end());
                
        function<void(int)> dfs = [&](int s) {
            ans.push_back(cur);
            for(int i = s; i < nums.size(); i++) {
                if(i != s && nums[i] == nums[i - 1]) continue;
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
