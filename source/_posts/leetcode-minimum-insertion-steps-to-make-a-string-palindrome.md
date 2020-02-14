---
title: LeetCode - Minimum Insertion Steps to Make a String Palindrome
date: 2019-12-01 13:26:31
tags:
- Algorithm
- DP
- String
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/KovlkXs.png
---

## [1312. Minimum Insertion Steps to Make a String Palindrome](https://leetcode.com/problems/minimum-insertion-steps-to-make-a-string-palindrome/)

Given a string s. In one step you can insert any character at any index of the string.

Return the minimum number of steps to make s palindrome.

A Palindrome String is one that reads the same backward as well as forward.

## Solution

$dp[i][j]$ := min insertions to make s[i ~ j] a palindrome

分兩種case討論，字串$S = s_{i} s_{i+1} ... s_{j}$
檢查$s_i$與$s_j$也就是字串頭尾字元是否一樣
1. 是，$dp[i][j]$的解就是$dp[i + 1][j - 1]$
2. 否，可以選擇在頭尾插入，方法數+1，取較小的
	* 頭插入$s_{j}$，所以插入方法數為$dp[i][j - 1] + 1$
	* 尾插入$s_{i}$，所以插入方法數為$dp[i + 1][j] + 1$

因為求解長度$k$的子字串，會要知道$k-1$、$k-2$長度的子字串方法數，外迴圈從長度$l=2$的子字串開始到長度$n$，最後$dp[0][n-1]$就是答案

<!-- more -->

## Code

```cpp
class Solution {
public:
    int minInsertions(string s) {
        
        const int n = s.length();
    
        
        vector<vector<int>> dp(n, vector<int>(n));
        
        for(int l = 2; l <= n; ++l)
            for(int i = 0, j = l - 1; j < n; ++i, ++j)
                dp[i][j] = (s[i] == s[j])? dp[i + 1][j - 1] : min(dp[i + 1][j], dp[i][j - 1]) + 1;
        
        
        return dp[0][n - 1];
        
    }
};
```

## Time Complexity

$O(n^2)$
