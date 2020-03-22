---
title: Leetcode - House Robber
date: 2020-03-11 08:13:15
tags:
- Algorithm
- Dynamic Programming
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/znhaIgh.jpg
---

## [198. House Robber](https://leetcode.com/problems/house-robber/)

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.

Example 1:

```
Input: [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.
```

<!-- more -->

## Solution 

第$i$間房子有$money[i]$的金錢，令$robbing_i$為搶劫$0$ ~ $i$間的最大獲利，答案為$robbing_{i-2} + money[i]$與$robbing_{i-1}$兩者取最大值，base case為$i == 0$時只有一間可以搶,答案為$money[0]$，$i == 1$時有2間可以搶，答案為$money[0]$與$money[1]$取最大值(因為不能連續搶)。


<img src="https://latex.codecogs.com/svg.latex?robbing_i&space;=&space;\begin{cases}&space;money[0]&space;&&space;\text{&space;if&space;}&space;i=0&space;\\&space;max(money[0],&space;money[1])&space;&&space;\text{&space;if&space;}&space;i=1&space;\\&space;max(robbing_{i-2}&space;&plus;&space;money[i],&space;robbing_{i-1})&space;&&space;\text{&space;otherwise&space;}&space;\end{cases}" alt="">


## <span style="color:red;">[TLE]</span> Method 1 - Recursion 

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        const int n = nums.size();
        if(n == 0) return 0;
        
        function<int(int)> robbing = [&](int i) {
            if(i == 0) return nums[0];
            if(i == 1) return max(nums[0], nums[1]);
            return max(robbing(i - 2) + nums[i], robbing(i - 1));
        };
        
        return robbing(n - 1);
    }
};
```

## Method 2 - DP (Memoization)



因為測資有$momey[i]$全是0的情況，而答案必為非負數，所以$memo[i]$初始化為-1，如果是非負代表已經存在$robbing_{i}$的解，直接回傳，否則遞迴求解並更新之。時間複雜度$(n)$

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        const int n = nums.size();
        if(n == 0) return 0;
        
        vector<int> memo(n, -1);
        
        function<int(int)> robbing = [&](int i) {
            if(i == 0) return nums[0];
            if(i == 1) return max(nums[0], nums[1]);
            if(memo[i] < 0) {
                memo[i] = max(robbing(i - 2) + nums[i], robbing(i - 1));
            }
            return memo[i];
        };
        
        return robbing(n - 1);
    }
};
```

> Memoization與recursion相同屬於top-down的求解過程，在過程中紀錄子問題答案避免重複求解。下面的Tabulation則是bottom-up從最小問題往上構成整個問題的答案。

## Method 3 - DP (Tabulation)


```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        if(nums.size() == 0) return 0;
        if(nums.size() == 1) return nums[0];
        if(nums.size() == 2) return max(nums[0], nums[1]);
        
        vector<int> dp(nums.size());
        dp[0] = nums[0];
        dp[1] = max(nums[0], nums[1]);       
        
        for(int i = 2; i < nums.size(); ++i) {
            dp[i] = max(dp[i - 2] + nums[i], dp[i - 1]);
        }        
        return dp.back();
    }
};
```