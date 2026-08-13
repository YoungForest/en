---
title: LeetCode weekly contest 138
date: 2019-05-26 11:27:38
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/05/26/LeetCode-weekly-contest-138/
  en: https://youngforest.github.io/en/2019/05/26/LeetCode-weekly-contest-138/
---
Although the problem quality this week was quite good, the difficulty was not high. It was a contest about speed.
Because the second problem had a relatively long statement, my problem-solving order was 1->3->4->2.

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (6) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 247 / 4143|	YoungForest | 	20 | 	0:57:43 | 0:11:19 | 0:52:43 (1) |	0:27:35  | 0:36:44 |

## 1051. Height Checker

Intuition:
Simple sorting, then traverse once and compare.
Time complexity: O(N log N)
Space complexity: O(N)

```cpp
class Solution {
public:
    int heightChecker(vector<int>& heights) {
        auto copy = heights;
        sort(copy.begin(), copy.end());
        int ret = 0;
        for (int i = 0; i < heights.size(); ++i) {
            if (copy[i] != heights[i])
                ++ret;
        }
        return ret;
    }
};
```

## 1052. Grumpy Bookstore Owner
This was the last problem I did, and it took quite a lot of time. The reason I could not get into the top 200 this time was first that I spent too long understanding the first problem, and second that I was too rushed on this problem. I wrote code directly, got the idea wrong, and only corrected it later. Then I also had one penalty on this problem because I forgot to update `max_wanhui` the first time. In fact, at the beginning I did remember that it needed to be updated, but I forgot while writing.
The lesson is: first think through the algorithm and confirm it is feasible before writing code. Also, write test cases first, especially corner cases, then think about the algorithm, and only then start writing. This can effectively verify the correctness of the algorithm and code.

Intuition:
Sliding window. Continuously update the maximum recoverable loss.

Time complexity: O(N).
Space complexity: O(1).


```cpp
class Solution {
public:
    int maxSatisfied(vector<int>& customers, vector<int>& grumpy, int X) {
        int n = customers.size();
        const int total_customers = accumulate(customers.cbegin(), customers.cend(), 0);
        int sunshi = 0;
        for (int i = 0; i < n; ++i) {
            if (grumpy[i] == 1)
                sunshi += customers[i];
        }
        int max_wanhui = 0;
        int wanhui = 0;
        for (int i = 0; i < X; ++i) {
            if (grumpy[i] == 1)
                wanhui += customers[i];
        }
        max_wanhui = max(max_wanhui, wanhui);
        
        for (int i = X; i < n; ++i) {
            if (grumpy[i] == 1)
                wanhui += customers[i];
            if (grumpy[i - X] == 1)
                wanhui -= customers[i - X];
            max_wanhui = max(max_wanhui, wanhui);
        }
        // cout << total_customers << " " << sunshi << " " << max_wanhui << endl;
        return total_customers - sunshi + max_wanhui;
    }
};
```

## 1053. Previous Permutation With One Swap

Intuition:
Greedy. We want to find the largest permutation sequence smaller than the current one that can be produced by one swap.
First, to make the permutation smaller, we must swap a large value with a small value.
Then, to make the swapped sequence as large as possible, the large value should be as far back as possible, and the small value should also be as far back as possible.

Time complexity: O(N),
Space complexity: O(N).

```cpp
class Solution {
public:
    vector<int> prevPermOpt1(vector<int>& A) {
        int larger_index = A.size() - 1;
        while (larger_index >= 1) {
            if (A[larger_index] >= A[larger_index - 1]) {
                --larger_index;
            } else {
                break;
            }
        }
        if (larger_index == 0)
            return A;
        --larger_index;
        auto insert_it = lower_bound(A.cbegin() + larger_index + 1, A.cend(), A[larger_index]);
        int swap_index = std::distance(A.cbegin(), insert_it) - 1;
        swap(A[swap_index], A[larger_index]);
        
        return A;
    }
};
```
## 1054. Distant Barcodes
Intuition:
Greedy. Each time, take the number with the largest frequency, while ensuring that this number is not the same as the previous number.
A **priority queue** happens to conveniently implement the requirement of taking the number with the largest frequency.
Time complexity: O(N log N), the complexity of the priority queue's basic operation `push` is `O(log N)`. If you are not familiar with STL algorithms and container adapters, I recommend [*The C++ Standard Library, 2nd Edition*](https://book.douban.com/subject/26419721/) again.
Space complexity: O(N).

```cpp
class Solution {
public:
    vector<int> rearrangeBarcodes(vector<int>& barcodes) {
        map<int, int> count;
        for (int barcode : barcodes) {
            ++count[barcode];
        }
        priority_queue<pair<int, int>> pq;
        for (const auto& p : count) {
            pq.push({p.second, p.first});
        }
        vector<int> ret;
        pair<int, int> old_top;
        while (!pq.empty()) {
            auto t = pq.top();
            pq.pop();
            ret.push_back(t.second);
            --t.first;
            if (old_top.first > 0)
                pq.push(old_top);
            old_top = t;
        }
        if (old_top.first > 0)
            ret.push_back(old_top.second);
        return ret;
    }
};
```
