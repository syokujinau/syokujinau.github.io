---
title: Leetcode - Maximum Subarray
date: 2020-01-11 13:25:31
tags:
- Algorithm
- Dynamic Programming
- Divide & Conquer
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/tJj4dyb.png
---

## [53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)



Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

Example:

```
Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```

<!-- more -->


### DP

$dp_i$：以$nums[i]$結尾的子陣列最大值
在$dp_i + nums[i]$與$nums[i]$取較大的，取前者代表$nums[i]$是正數，取後者代表$nums[i]$負數，以此為起點繼續下去

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        vector<int> dp(nums.size());
        dp[0] = nums[0];
        int MAX = dp[0];
        for(int i = 1; i < nums.size(); ++i) {
            dp[i] = max(dp[i - 1] + nums[i], nums[i]);
            MAX = (dp[i] > MAX)? dp[i] : MAX;
        }
        return MAX;
    }
};
```

### Complexity

time：$O(n)$
space：$O(n)$

## DP: $O(1)$ space

目前狀態只跟前一個狀態有關，答案也只須紀錄歷史最大值，只要用單一變數紀錄即可。

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int dp = nums[0];
        int ans = dp;
        for(int i = 1; i < nums.size(); ++i) {
            dp = max(dp + nums[i], nums[i]);
            ans = (dp > ans)? dp : ans;
        }
        return ans;
    }
};
```


## Divide and Conquer

pseudocode: [演算法設計 - Divide and Conquer](https://syokujinau.github.io/2019/03/13/algorithm-design-divide-and-conquer/)
概念是分成3部份，中間index為$m$，從以下三者找最大值
1. $l$ ~ $m - 1$最大的子陣列（遞迴）
2. $m + 1$ ~ $r$最大的子陣列（遞迴）
3. $m$為中心左右擴展最大的子陣列

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        
        function<int(int, int)> recur = [&] (int l, int r) {
            if (l > r) {
                return INT_MIN;
            }

            int m = l + (r - l) / 2

            int m2l = 0; 
            for (int i = m - 1, sum = 0; i >= l; i--) {
                sum += nums[i];
                m2l = max(sum, m2l);
            }
            
            int m2r = 0;
            for (int i = m + 1, sum = 0; i <= r; i++) {
                sum += nums[i];
                m2r = max(sum, m2r);
            }

            return max({
                        recur(l, m - 1), 
                        recur(m + 1, r), 
                        m2l + m2r + nums[m]
                        });
        };
        
        return recur(0, nums.size() - 1);
    }
};
```

### Complexity

$T(n) = 2T(\frac{n}{2}) + \Theta (n)$

$= \Theta (nlogn)$ ([Master theorem](https://syokujinau.github.io/2019/02/28/introduction-to-asymptotic-notation/#more) case 2)


