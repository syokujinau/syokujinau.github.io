---
title: Leetcode - Iterator for Combination
date: 2020-01-06 13:41:11
tags:
- Algorithm
- Backtracking
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/2bcBdqb.png
---

## [1286. Iterator for Combination](https://leetcode.com/problems/iterator-for-combination/)

Design an Iterator class, which has:

* A constructor that takes a string characters of sorted distinct lowercase English letters and a number combinationLength as arguments.
* A function next() that returns the next combination of length combinationLength in lexicographical order.
* A function hasNext() that returns True if and only if there exists a next combination.
 
Example:

```
CombinationIterator iterator = new CombinationIterator("abc", 2); // creates the iterator.

iterator.next(); // returns "ab"
iterator.hasNext(); // returns true
iterator.next(); // returns "ac"
iterator.hasNext(); // returns true
iterator.next(); // returns "bc"
iterator.hasNext(); // returns false
```



<!-- more -->

Constraints:

* `1 <= combinationLength <= characters.length <= 15`
* There will be at most `10^4` function calls per test.
* It's guaranteed that all calls of the function `next` are valid.

## Solution

$C^n_r$組合數為$\frac{n!}{(n-r)!r!}$
參數`characters`的長度是$n$，`combinationLength`就是$r$
建構子`CombinationIterator`被呼叫時用dfs搜尋生成所有組合，當遞迴深度$d$到達$r$就是leaf，紀錄並回傳

```cpp
class CombinationIterator {
public:
    
    vector<string> ans;
    int idx;
    
    CombinationIterator(string characters, int combinationLength) {
        
        idx = 0;
        string cur = "";
        
        
        function<void(int, int)> dfs = [&] (int d, int s) {
            if(d == combinationLength) {
                ans.push_back(cur);
                return;
            }
            
            for(int i = s; i < characters.size(); ++i) {
                cur.push_back(characters[i]);
                dfs(d + 1, i + 1);
                cur.pop_back();
            }
        };
        
        dfs(0, 0);
        
        // for(string s : ans) cout << s << " "; cout << endl;
    }
    
    string next() {
        return ans[idx++];
    }
    
    bool hasNext() {
        return idx < ans.size();
    }
};

/**
 * Your CombinationIterator object will be instantiated and called as such:
 * CombinationIterator* obj = new CombinationIterator(characters, combinationLength);
 * string param_1 = obj->next();
 * bool param_2 = obj->hasNext();
 */
```