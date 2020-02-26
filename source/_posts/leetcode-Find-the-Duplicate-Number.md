---
title: Leetcode - Find the Duplicate Number
date: 2020-01-02 06:06:21
tags:
- Algorithm
- Array
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/NvYQ0Eo.png
---


## [287. Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)

Given an array nums containing n + 1 integers where each integer is between 1 and n (inclusive), prove that at least one duplicate number must exist. Assume that there is only one duplicate number, find the duplicate one.

Example 1:

```
Input: [1,3,4,2,2]
Output: 2
```

Example 2:

```
Input: [3,1,3,4,2]
Output: 3
```

Note:

* You must not modify the array (assume the array is read only).
* You must use only constant, O(1) extra space.
* Your runtime complexity should be less than O(n2).
* There is only one duplicate number in the array, but it could be repeated more than once.


<!-- more -->

## Solution 1

主要限制在於空間複雜度O(1)，不能用其他陣列標記重複數字
因為數字只有$1$ ~ $n-1$，所以一定能放到正確位置，例如$3$的正確位置是index = $2$，$nums[i]$的正確位置是index = $nums[i] - 1$
所以將數字對調之前檢查$nums[nums[i] - 1]$與$nums[i]$是否相等，若成立則找到重複字。

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        for(int i = 0; i < nums.size(); ++i) {
            while(i + 1 != nums[i]) {
                if(nums[nums[i] - 1] == nums[i]) return nums[i];
                swap(nums[i], nums[nums[i] - 1]);
            }
        }
        return -1; // for compiling
    }
};
```

## Solution 2

因為元素皆為正數，且元素減1都可以對應到合法的index，因此在對應的index元素加負號作為標記，若在標記時發現該位置已經是負數代表曾經出現過了。

做法相同的題目還有[442. Find All Duplicates in an Array](https://leetcode.com/problems/find-all-duplicates-in-an-array/)

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        for(int i = 0; i < nums.size(); ++i) {
            int idx = abs(nums[i]) - 1;
            if(nums[idx] < 0) return idx + 1;
            nums[idx] *= -1;
        }
        return -1; // for compiling
    }
};
```


## Complexity

Time :$O(n)$
Space: $O(1)$