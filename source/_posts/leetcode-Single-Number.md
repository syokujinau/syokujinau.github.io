---
title: Leetcode - Single Number
date: 2019-11-20 10:23:31
tags:
- Algorithm
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/u66DsYI.jpg
---

## [136. Single Number](https://leetcode.com/problems/single-number/)

Given a non-empty array of integers, every element appears twice except for one. Find that single one.

Note:

Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

Example 1:


```
Input: [2,2,1]
Output: 1
```

<!-- more -->

## Solution

利用以下性質，來找到唯一不成對的數字
1. 任何boolean variable自己與自己xor為0
2. xor具有交換律(Commutative)，所以不管如何打亂順序都不影響結果

## Code

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int x = 0;
        for(int el : nums) x ^= el;
        return x;
    }
};
```

## Time Complexity

$O(n)$