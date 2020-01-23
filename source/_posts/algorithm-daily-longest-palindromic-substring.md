---
title: Algorithm Daily - Longest Palindromic Substring
date: 2019-12-23 22:26:31
tags:
- Algorithm
- Tree
- BFS
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/L7Ap4sE.png
---

## 5. Longest Palindromic Substring

求最長迴文子字串


Example 1:
```
Input: "babad"
Output: "bab"
```

Example 2:
```
Input: "cbbd"
Output: "bb"
```
<!-- more -->
## Solution

初始設最大值max_len為0
i從0到n
以下兩個case的迴文子字串長度取較大的值 

1. 奇數長度迴文子字串以s[i]為中心擴散 (e.g. "aba", i = 1)
2. 偶數長度迴文子字串以s[i]、s[i + 1]擴散 (e.g. "abba", i = 1)

若比max_len則更新，並用start紀錄該迴文子字串起始點

## Code

```cpp
class Solution {
public:
    string longestPalindrome(string s) {

        
        auto getLength = [&](int i, int j) { 
            // return the length of longest palindromic substring
            while(i>= 0 && j < s.size() && s[i] == s[j]) {
                i--;
                j++;
            }
            return j - i - 1;
        };
    
        int max_len = 0;
        int start = 0;
        
        for(int i = 0; i < s.size(); ++i) { // O(n)

            int cur = max(getLength(i, i), getLength(i, i + 1)); // O(n)
            
            if(cur > max_len) {
                max_len = cur;
                start = i - (max_len - 1) / 2;
            }
            
        }
    
        return s.substr(start, max_len);
    }
    
    
};
```

### Time complexity

$O(n^2)$

## Note

用C++11 Lambda來簡化寫法，參考[C++11 Lambda Expression 語法教學與範例](https://blog.gtwang.org/programming/lambda-expression-in-c11/)

