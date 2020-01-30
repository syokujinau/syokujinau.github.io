---
title: Leetcode - Sliding Window Maximum
date: 2020-01-10 15:41:40
tags:
- Algorithm
- BST
- Multiset STL
- Queue
- Deque STL
- Sliding Window
categories:
- [Algorithm, LeetCode]
thumbnail: https://i.imgur.com/XjzWrok.png
---


## [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)

## Method 1 - Brute force

這題測資很弱，暴力解也給過= =

使用`std::max_element(nums.begin() + i, nums.begin() + j)`，回傳i到j(不含)的最大值iterator

```cpp 
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        const int n = nums.size();
        vector<int> ans;
        
        // empty case
        if(n == 0) return ans;
        
        for(int i = 0, j = k - 1; j < n; ++i, ++j) {
            auto max = max_element(nums.begin() + i, nums.begin() + j + 1);
            ans.push_back(*max);
        }
        
        return ans;
    }
};
```

<!-- more -->

### Complexity

Time: $O(n*k)$
Space: $O(1)$


## Method 2 - BST

```cpp
class Solution {
public:
  vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    vector<int> ans;
    if (nums.size() == 0) return ans;
     
    // Pick first k elements and create a Self-Balancing Binary Search Tree (BST) of size k.
    multiset<int, less <int>> window(nums.begin(), nums.begin() + k);

      
    for(int i = 0, j = k ; j < nums.size(); ++i, ++j) {
        ans.push_back(*window.rbegin()); // get maximum of BST
        window.insert(nums[j]); // add nums[j]
        window.erase(window.equal_range(nums[i]).first); // Search for nums[i] in the BST and delete it from the BST.
    }
    
    // get final tree maximum  
    ans.push_back(*window.rbegin());
      
    return ans;
  }
};
```

### Complexity

Time: $O(nlogn)$
Space: $O(k)$

## Method 3 - Deque

Monotonic Queue: 單調遞減佇列


```cpp
class MonotonicQueue {
public:
	// pop掉所有比e小的元素，並push back
	void push(int e); // amortize time: O(1)
	// pop掉最大值(因最大在front所以pop_front)
	void pop();
	// peek最大值
	int max() const;
private:
	deque<int> dq;
};

void MonotonicQueue::push(int e) {
	while(!dq.empty() && e > dq.back()) dq.pop_back();
	dq.push_back(e);
}

void MonotonicQueue::pop() {
	dq.pop_front();
}

int MonotonicQueue::max() const {
	return dq.front();
}


class Solution {
public:
	vector<int> maxSlidingWindow(vector<int>& nums, int k) {
		vector<int> ans;
		
		MonotonicQueue mq;

		for(int i = 0; i < nums.size(); i++) {
			mq.push(nums[i]);
			if(i - k + 1 >= 0) { // window start index, record answer
				ans.push_back(mq.max());
				if(nums[i - k + 1] == mq.max()) mq.pop(); // next window doesn't include first element of current window
			}
		}

		return ans;
	}
};
```
### Complexity

Time: $O(n)$
Space: $O(k)$


## Conclusion

| Method| Time | Space| Run time|
|---|---|---|---|
|Brute force|O(nk)  |O(1)|220 ms|
|BST        |O(nlogn)|O(k)|124 ms|
|Deque      | O(n)   |O(k)|84 ms|



## Reference

* [HackerRank - Deque-STL](https://www.hackerrank.com/challenges/deque-stl/problem?utm_campaign=challenge-recommendation&utm_medium=email&utm_source=7-day-campaign)
* [GeeksforGeeks - Sliding Window Maximum (Maximum of all subarrays of size k)](https://www.geeksforgeeks.org/sliding-window-maximum-maximum-of-all-subarrays-of-size-k/#disqus_thread)
* [Youtube - Hua Hua](https://www.youtube.com/watch?v=2SXqBsTR6a8)

