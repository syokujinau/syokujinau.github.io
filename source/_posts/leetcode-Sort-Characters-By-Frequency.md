---
title: Leetcode - Sort Characters By Frequency
date: 2020-02-16 11:46:47
tags:
- Algorithm
- Priority Queue
- Hash Table
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/t8tlrdP.jpg
---


## [451. Sort Characters By Frequency](https://leetcode.com/problems/sort-characters-by-frequency/)\

Given a string, sort it in decreasing order based on the frequency of characters.

Example 1:

```
Input:
"tree"

Output:
"eert"

Explanation:
'e' appears twice while 'r' and 't' both appear once.
So 'e' must appear before both 'r' and 't'. Therefore "eetr" is also a valid answer.
```

Example 2:

```
Input:
"cccaaa"

Output:
"cccaaa"

Explanation:
Both 'c' and 'a' appear three times, so "aaaccc" is also a valid answer.
Note that "cacaca" is incorrect, as the same characters must be together.
```

<!-- more -->

Example 3:

```
Input:
"Aabb"

Output:
"bbAa"

Explanation:
"bbaA" is also a valid answer, but "Aabb" is incorrect.
Note that 'A' and 'a' are treated as two different characters.
```

## Solution



```cpp
class Solution {
public:
    string frequencySort(string s) {
        
        unordered_map<char, int> freq;
        priority_queue<pair<int, char>> pq;
        
        for (char c : s) {
            freq[c]++;
        }
        
        for (auto [key, val] : freq) { // O(nlogn)
            pq.emplace(pair<int, char> (val, key));
        }
        
        string ans;
        
        while (!pq.empty()) {
            auto top = pq.top(); // O(n)
            for (int i = 0; i < top.first; i++) {
                ans += top.second;
            }
            pq.pop(); // O(logn)
        }
        
        return ans;
    }
};
```