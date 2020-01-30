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


<!-- ## LeetCode - 17. Letter Combinations of a Phone Number -->

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

DFS(text, idx)

按鍵對應第$idx$個字母

<img src="https://i.imgur.com/eVd46af.png" width="50%" />

## Code

```cpp
class Solution {
public:
    
    vector<string> ans;
    int n;
    string _digits;
    vector<string> key;

    void dfs(string text, int idx) {
        if(idx == n) {
            ans.push_back(text);
            return;
        }

        string letters = key[_digits[idx] - '0'];

        for(int i = 0; i < letters.size(); i++) {
            text.push_back(letters[i]);
            dfs(text, idx + 1);
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