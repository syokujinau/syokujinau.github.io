---
title: Leetcode - Find First and Last Position of Element in Sorted Array
date: 2020-02-01 23:13:01
tags:
- Algorithm
- Binary Search
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/3f60CAV.png
---


## [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)


Given an array of integers `nums` sorted in ascending order, find the starting and ending position of a given `target` value.

Your algorithm's runtime complexity must be in the order of $O(log n)$.

If the target is not found in the array, return `[-1, -1]`.

Example 1:

```
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]
```

<!-- more -->

## Solution

本題提示：

* sorted 
* target
* O(log n)

明顯提示是用Binary Search... 如果有其他方法歡迎下面留言：）

本來想在一個Binary Search中同時遞迴找起始與結束的index，但是如果nums與target都是同一個數字例如[5,5,5,5,5], target = 5，此時就會變成$O(n)$造成超時，所以用2個Binary Search，分別找起始與結束，總體還是$O(logn)$。


```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        
        const int n = nums.size();
        
        vector<int> ans{-1, -1};
        
        function<void(int, int)> findStartIndex = [&] (int l, int r) {
            if(l > r) return;
            
            int mid = (r + l) / 2;
            if(nums[mid] == target) {
                ans[0] = mid;
                if(mid > 0 && nums[mid - 1] == target) findStartIndex(0, mid - 1);
            }
            else if (nums[mid] > target) findStartIndex(0, mid - 1);
            else                         findStartIndex(mid + 1, r);
        };
        
        function<void(int, int)> findEndIndex = [&] (int l, int r) {
            if(l > r) return;
            
            int mid = (r + l) / 2;
            if(nums[mid] == target) {
                ans[1] = mid;
                if(mid < n - 1 && nums[mid + 1] == target) findEndIndex(mid + 1, r);
            }
            else if (nums[mid] > target) findEndIndex(0, mid - 1);
            else                         findEndIndex(mid + 1, r);
        };
                
        findStartIndex(0, n - 1);
        findEndIndex(0, n - 1);
        
        return ans;
    }
};
```

## Time complexity

$O(log n)$