---
title: LeetCode - Letter Combinations of a Phone Number
date: 2019-12-16 11:16:47
tags:
- Algorithm
- Backtracking
- DFS
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/jhZIzAu.png
---


## [17. Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)

Given a string containing digits from *2-9* inclusive, return all possible letter combinations that the number could represent.

A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

<img src="https://i.imgur.com/gUjCRCX.png" width="33%" />

Example:

```
Input: "23"
Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
Note:
```

Although the above answer is in lexicographical order, your answer could be in any order you want.

<!-- more -->

## Solution

```
DFS(text, i)
```

$text$ := 目前字串
$digits_i$ := 數字鍵，對應字母從key取得，例如"2"對應"abc"

<img src="https://i.imgur.com/x3scwxE.png" />

## Code

```cpp
class Solution {
public:
    
    vector<string> ans;
    int n;
    string _digits;
    vector<string> key;

    void dfs(string text, int i) {
        if(i == n) { // leaf 
            ans.push_back(text);
            return; 
        }

        string letters = key[_digits[i] - '0'];

        for(char c : letters) {
            text.push_back(c);
            dfs(text, i + 1);
            text.pop_back();
        }

    }    
    
    vector<string> letterCombinations(string digits) {
        
        n = digits.size();
        _digits = digits;
        
        if(n == 0) return ans;
        
        key.push_back("");
        key.push_back("");
        key.push_back("abc");  // 2
        key.push_back("def");  // 3
        key.push_back("ghi");  // 4
        key.push_back("jkl");  // 5
        key.push_back("mno");  // 6
        key.push_back("pqrs"); // 7
        key.push_back("tuv");  // 8
        key.push_back("wxyz"); // 9
         
        
        dfs("", 0);
        
        return ans;
    }
};
```

## Time complexity

就是整個tree的node數
令$digits$長度$n$
複雜度約為公比$3$等比級數前$n$項的和
$\therefore O(3^n)$