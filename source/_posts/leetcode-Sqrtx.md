---
title: Leetcode - Sqrt(x)
date: 2019-10-25 20:31:16
tags:
- Algorithm
- Binary Search
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/acDnDar.png
---


## [69. Sqrt(x)](https://leetcode.com/problems/sqrtx/)


Implement `int sqrt(int x)`.

Compute and return the square root of x, where x is guaranteed to be a non-negative integer.

Since the return type is an integer, the decimal digits are truncated and only the integer part of the result is returned.

Example 1:

```
Input: 4
Output: 2
```

Example 2:

```
Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since 
             the decimal part is truncated, 2 is returned.
```

<!-- more -->


## Solution

$\sqrt{x}$一定在$1$ ~ $x$之間，二分搜尋這個範圍

以下方法避免integer overflow
* 使用`int mid = l + (r - l) / 2;`而不是`int mid = (l + r) / 2`
* 檢查`x / mid == mid`而不是檢查`mid * mid == x`
當非法邊界`l > r`成立時，$r$是答案

```cpp
class Solution {
public:
    int mySqrt(int x) {
                
        function<int(int, int)> sqrt = [&] (int l, int r) {
            if(l > r)              return r;            
            int mid = l + (r - l) / 2;
            if(x / mid == mid)     return mid;
            else if(x / mid < mid) return sqrt(l, mid - 1);
            else                   return sqrt(mid + 1, r); 
        }; 
        
        return sqrt(1, x);
        
    }
};
```

## Time Complexity

$O(log n)$