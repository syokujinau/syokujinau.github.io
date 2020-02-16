---
title: Leetcode - Two Sum II - Input array is sorted
date: 2019-10-19 17:01:16
tags:
- Algorithm
- Binary Search
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/YBExHTk.png
---

## [167. Two Sum II - Input array is sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

Given an array of integers that is already sorted in ascending order, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2.

Note:

Your returned answers (both index1 and index2) are not zero-based.
You may assume that each input would have exactly one solution and you may not use the same element twice.

Example:

```
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore index1 = 1, index2 = 2.
```

<!-- more -->

## Solution

輸入陣列已經sorted，可以直接二分搜尋，針對第i個元素，搜尋index = (i + 1) ~ (n - 1)

## Code

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        
        function<int(int, int, int)> binarySearch = [&](int l, int r, int k) {
            if(l <= r) {
                int mid = (l + r) / 2;
                if(numbers[mid] == k)      return mid;
                else if (numbers[mid] > k) return binarySearch(l, mid - 1, k);
                else                       return binarySearch(mid + 1, r, k);
            }  
            return -1;
        };
        
        for(int i = 0; i < numbers.size() - 1; ++i) { // O(nlogn)
            int result = binarySearch(i + 1, numbers.size() - 1, target - numbers[i]);
            if(result != -1) return {i + 1 , result + 1};
        }
        
        return {};
        
    }
};
```

## Time Complexity

$O(nlogn)$