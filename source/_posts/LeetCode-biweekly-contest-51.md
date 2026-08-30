---
title: LeetCode Biweekly Contest 51
date: 2021-05-02 11:52:18
description: Four solutions shift alternating characters, manage seats with a min-heap, maximize a capped increasing array, and answer closest-room queries offline.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/05/02/LeetCode-biweekly-contest-51/
  en: https://youngforest.github.io/en/2021/05/02/LeetCode-biweekly-contest-51/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 150 / 9378 | YoungForest | 18 | 0:32:04 |  0:05:26 |  0:07:22 |  0:11:24 | 0:32:04 |

A hand-speed contest. Recently my hand speed is far worse than before, and the last problem also took quite a bit of time because I was not familiar enough with it.

Actually, in a hand-speed contest, the algorithms for all problems are not hard. Thinking of the correct solution is quick, but implementing it quickly and bug-free tests every programmer's fundamentals.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-biweekly-contest-51/en-hero.webp" alt="Alternating tokens pass through shift gears, a heap dispenser returns the smallest seat token, sorted stones become a gently rising staircase and eligible room keys gather around a preferred position" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 1844. Replace All Digits with Characters

A warm-up problem. Just follow the problem statement.

```cpp
class Solution {
    char shift(char c, int x) {
        return c + x;
    }
public:
    string replaceDigits(string s) {
        for (int i = 1; i < s.size(); i += 2) {
            s[i] = shift(s[i-1], s[i] - '0');
        }
        return s;
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1845. Seat Reservation Manager

Because each operation finds the smallest seat number, and there is also an `insert` (`unreserve`) operation, it is obvious that a `priority_queue` / Heap is needed to implement this requirement.

```cpp
class SeatManager {
    priority_queue<int, vector<int>, greater<>> pq;
public:
    SeatManager(int n) {
        for (int i = 1; i <= n; ++i) {
            pq.push(i);
        }
    }
    
    int reserve() {
        const int ans = pq.top();
        pq.pop();
        return ans;
    }
    
    void unreserve(int seatNumber) {
        pq.push(seatNumber);
    }
};

/**
 * Your SeatManager object will be instantiated and called as such:
 * SeatManager* obj = new SeatManager(n);
 * int param_1 = obj->reserve();
 * obj->unreserve(seatNumber);
 */
```

Time complexity:

- SeatManager: O(n),
- reserve: O(log n),
- unreserve: O(log n).

Space complexity: O(n).

## 1846. Maximum Element After Decreasing and Rearranging

Greedy.

Observing the two operations, `Decrease` and `Rearrange`, we can see that actually only one `Rearrange` is needed. When doing `Decrease`, prioritize reducing smaller values. Because larger values can always cover smaller ones after decreasing, but smaller values cannot do the reverse.

```cpp
class Solution {
public:
    int maximumElementAfterDecrementingAndRearranging(vector<int>& arr) {
        const int n = arr.size();
        sort(arr.begin(), arr.end());
        int last = 0;
        for (int i = 0; i < n; ++i) {
            if (arr[i] > last) {
                arr[i] = last + 1;
            } else {
                arr[i] = last;
            }
            last = arr[i];
        }
        return arr[n - 1];
    }
};
```

Time complexity: O(n log n + n),
space complexity: O(1).

## 1847. Closest Room

The first thought is a brute-force solution: for each `query`, traverse all `rooms` once to find the answer. The time complexity is `O(k * n)`, which will obviously TLE.

The intended solution should have a form like `O(n log n)`.

Here we need a so-called **offline query** technique.

Online computation means the answer order of `queries` is unchanged, similar to a function that is called once and answers once.

Offline computation means the answer order of `queries` can be changed, and an array of `queries` needs to be solved all at once. At this time, we can reorder `queries` to get an amortized faster algorithm.

First, sort `queries` by `minisize`, and sort `rooms` by `size`.

Use two pointers to ensure that for the current `query`, all `rooms` satisfying the requirement have been added to the candidate set. Here we use a `TreeSet` to maintain the candidate set, so the nearest `room` can be searched in `log n`.


```cpp
class Solution {
public:
    vector<int> closestRoom(vector<vector<int>>& rooms, vector<vector<int>>& queries) {
        // brute force: k * n
        // need: k * log n + n log n
        // online, sort queries by minisize from big to small, find close candidate
        const int n = rooms.size();
        sort(rooms.begin(), rooms.end(), [&](const auto& a, const auto& b) -> bool {
            return a[1] > b[1];
        });
        const int k = queries.size();
        using pii = pair<int, int>;
        vector<pii> searchOrder; // miniSize, index
        searchOrder.reserve(k);
        for (int i = 0; i < k; ++i) {
            searchOrder.push_back({queries[i][1], i});
        }
        sort(searchOrder.begin(), searchOrder.end(), [&](const auto& a, const auto& b) -> bool {
            return a.first > b.first;
        });
        int i = 0;
        set<int> candidate;
        vector<int> ans(k, -1);
        for (int j = 0; j < k; ++j) {
            while (i < n && rooms[i][1] >= searchOrder[j].first) {
                candidate.insert(rooms[i++][0]);
            }
            const int prefer = queries[searchOrder[j].second][0];
            if (!candidate.empty()) {
                auto it = candidate.lower_bound(prefer);
                if (it == candidate.begin()) {
                    ans[searchOrder[j].second] = *it;
                } else if (it == candidate.end()) {
                    ans[searchOrder[j].second] = *prev(it);
                } else {
                    if (abs(*it - prefer) < (prefer - *prev(it))) {
                        ans[searchOrder[j].second] = *it;
                    } else {
                        ans[searchOrder[j].second] = *prev(it);
                    }
                }
            } else {
                ans[searchOrder[j].second] = -1;
            }
        }
        return ans;
    }
};
```

Time complexity: O(n log n + k log n + k log k),
space complexity: O(k + n).
