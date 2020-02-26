---
title: Leetcode - Find All Duplicates in an Array
date: 2020-01-04 16:36:20
tags:
- Algorithm
- Array
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/P9YKQjx.png
---



## [442. Find All Duplicates in an Array](https://leetcode.com/problems/find-all-duplicates-in-an-array/)


Given an array of integers, 1 ≤ a[i] ≤ n (n = size of array), some elements appear twice and others appear once.

Find all the elements that appear twice in this array.

Could you do it without extra space and in O(n) runtime?

Example:
Input:
[4,3,2,7,8,2,3,1]

Output:
[2,3]

<!-- more -->

## Solution

因為元素皆為正數，且元素減1都可以對應到合法的index，因此在對應的index元素加負號作為標記，若在標記時發現該位置已經是負數代表曾經出現過了。

做法相同的題目還有[287. Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)

```cpp
class Solution {
public:
    vector<int> findDuplicates(vector<int>& nums) {
        vector<int> ans;
        for(int i = 0; i < nums.size(); ++i) {
            int idx = abs(nums[i]) - 1;
            if(nums[idx] < 0) ans.push_back(idx + 1);
            nums[idx] *= -1;
        }
        return ans;  
    }
};
```

## Complexity

Space: $O(1)$
Time: $O(n)$