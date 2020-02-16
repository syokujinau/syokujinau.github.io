---
title: Leetcode - First Missing Positive
date: 2019-10-19 19:01:16
tags:
- Algorithm
- Array
- Hash Table
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/8PIoWRV.png
---

Stripe的面試題

## [41. First Missing Positive](https://leetcode.com/problems/first-missing-positive/)

Given an unsorted integer array, find the smallest missing positive integer.

Example 1:

```
Input: [1,2,0]
Output: 3
```

Example 2:

```
Input: [3,4,-1,1]
Output: 2
```

Note:

Your algorithm should run in O(n) time and uses constant extra space.

<!-- more -->

## Method 1 

查找不存在元素，想到用hashing的方法解，以下方法可被接受，時間複雜度也在$O(n)$，但題目希望空間複雜度要在$O(1)$

```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        unordered_set<int> us;
        for(int el : nums) us.insert(el);
        
        int i = 1;
        while(i <= nums.size() + 1) {
            // cout << "i = " << i <<endl; 
            if(us.find(i) == us.end()) break;
            i++;
        }
        
        return i;
    }
};
```

## Method 2

若陣列長度為n，答案必為1 ~ n，陣列每個元素放到正確位置後，掃描陣列，若有元素放在錯誤位置即為first missing positive。

<img src="https://i.imgur.com/KWujnBh.png" width="85%" />



```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        const int n = nums.size();

        for(int i = 0; i < nums.size(); ++i) {
            while(nums[i] > 0 && nums[i] <= n && nums[nums[i] - 1] != nums[i]){
               swap(nums[i], nums[nums[i] - 1]);
            }
        }
        
        for(int i = 0; i < nums.size(); ++i) {
            if(i + 1 != nums[i]) return i + 1;
        }
        
        return nums.size() + 1;
    }    
};
```

時間同樣為$O(n)$，in-place操作所以空間複雜度$O(1)$