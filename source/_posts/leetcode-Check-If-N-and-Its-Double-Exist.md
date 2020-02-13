---
title: Leetcode - Check If N and Its Double Exist
date: 2019-10-21 12:26:31
tags:
- Algorithm
- Binary Search
- Hash Table
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/kaAQ5tH.png
---


### [1346. Check If N and Its Double Exist](https://leetcode.com/problems/check-if-n-and-its-double-exist/)


Given an array `arr` of integers, check if there exists two integers $N$ and $M$ such that $N$ is the double of $M$ ( i.e. $N = 2 
\times M$).

More formally check if there exists two indices `i` and `j` such that :

* `i != j`
* `0 <= i, j < arr.length`
* `arr[i] == 2 * arr[j]`



測資應考慮
1. 有負數
2. 整個array只有一個0, 應該回傳false
3. 整個array有多個0, 應回傳true

<!-- more -->

## Method 1 - Binary Search

有std::binary_search可以直接使用，但以下自行實作

```cpp
class Solution {
public:
    bool checkIfExist(vector<int>& arr) {
        
        // check if multiple 0 exist, the answer should be true.
        int cnt = 0;
        for(int el : arr) if(el == 0) cnt++; // O(n)
        if(cnt > 1) return true;
        
        // sorting for binary search
        sort(arr.begin(), arr.end()); // O(nlogn)
        
        // binary search
        function<int(int, int, int)> binary_search = [&](int l, int r, const int target) {
            if(l <= r) {
                int mid = l + (r - l) / 2;
                if (arr[mid] == target)     return mid;
                else if (arr[mid] > target) return binary_search(l, mid - 1, target);
                else                        return binary_search(mid + 1, r, target);
            }
            return -1;
        };
        
        for(int el : arr) { // O(n)
            if(el == 0) continue; // don't check the zero element
            if(binary_search(0, arr.size() - 1, 2 * el) != -1) return true; // O(logn)
        }
        
        return false;
        
    }
};
```

### Complexity

$O(nlogn)$

## Method 2 - Hash table


```cpp
class Solution {
public:
    bool checkIfExist(vector<int>& arr) {
        
        unordered_map<int, int> um;
        for(int el : arr) um[el]++; // O(n)
        
        // return true if multiple zero exist.
        if(um.find(0) != um.end() && um[0] > 1) return true; // O(1)
                
        for(int el : arr) { // O(n)
            if(el == 0) continue;
            if(um.find(2 * el) != um.end()) return true;
        }
        return false;
    }
};
```

### Complexity

$O(n)$