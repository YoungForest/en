---
title: LeetCode Weekly Contest 237
date: 2021-04-18 16:55:22
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/04/18/LeetCode-weekly-contest-237/
  en: https://youngforest.github.io/en/2021/04/18/LeetCode-weekly-contest-237/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 345 / 11446 | YoungForest | 18 | 0:40:04 | 0:03:21 | 0:05:10 | 0:25:30 | 0:35:04  1 |

It had been a long time since I solved all four problems and entered the top 500. Finally I can skip check-in.

I had been checking in for seven consecutive weeks and was almost unable to take it anymore. Recently LeetCode's difficulty has risen quite a bit, and many strong players have joined too. Achieving both four solved problems and top 500 is indeed not easy.

Today's hand speed also counted as a normal performance.

## 1832. Check if the Sentence Is Pangram

A warm-up problem.

Count the number of times each word appears and check whether all are greater than 0.

```cpp
class Solution {
public:
    bool checkIfPangram(string sentence) {
        vector<int> cnt(26, 0);
        for (char c : sentence) {
            ++cnt[c - 'a'];
        }
        for (int i : cnt) {
            if (i == 0) return false;
        }
        return true;
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1833. Maximum Ice Cream Bars

Greedy. Buy the cheap ones first.

```cpp
class Solution {
public:
    int maxIceCream(vector<int>& costs, int coins) {
        sort(begin(costs), end(costs));
        int ans = 0;
        for (int i : costs) {
            if (coins >= i) {
                ++ans;
                coins -= i;
            } else break;
        }
        return ans;
    }
};
```

Time complexity: O(costs.length * log cost.length),
space complexity: O(log cost.length).

## 1834. Single-Threaded CPU

Use a priority queue to choose which task to execute. According to the problem statement, tasks need to be picked in the order of `processTime, index`.

In addition, tasks need to be sorted by `enqueueTime` and added into the waiting priority queue.

There is a pitfall in C++ for this problem.

`tasks.length == n`

`1 <= n <= 10^5`

`1 <= enqueueTimei, processingTimei <= 10^9`

Therefore, the final time can exceed the `int` range, and `long long` is needed.

Of course, choosing `Python` avoids this problem.

```cpp
class Solution {
    using ll = long long;
public:
    vector<int> getOrder(vector<vector<int>>& tasks) {
        using pii = pair<ll, int>; // processTime, index
        // events
        using tii = tuple<ll, ll, ll>;  // enqueueTime, processTime, index
        vector<tii> events;
        events.reserve(tasks.size());
        for (int i = 0; i < tasks.size(); ++i) {
            events.push_back({tasks[i][0], tasks[i][1], i});
        }
        sort(begin(events), end(events));
        vector<int> ans;
        ans.reserve(tasks.size());
        ll now = 0;
        int i = 0;
        priority_queue<pii, vector<pii>, greater<>> wait; 
        while (i < events.size() || !wait.empty()) {
            while (i < events.size() && get<0>(events[i]) <= now) {
                wait.push({get<1>(events[i]), get<2>(events[i])});
                ++i;
            }
            if (wait.empty()) {
                wait.push({get<1>(events[i]), get<2>(events[i])});
                now = get<0>(events[i]);
                ++i;
            }
            auto run = wait.top();
            wait.pop();
            ans.push_back(run.second);
            now += run.first;
        }
        return ans;
    }
};
```

Time complexity: O(N log N), N = tasks.length,
space complexity: O(N).

## 1835. Find XOR Sum of All Pairs Bitwise AND

At first glance, the problem feels hard to start. I could only think of a brute-force method, enumerating all pairs, whose time complexity is obviously not enough: `arr1.length * arr2.length = 10^10`.

But if we think more carefully, for this kind of bit operation, every bit is independent. We can simplify the problem to a specific bit. Once focusing on a single bit, the solution becomes clear.

We only need to count the number of 0s and 1s in `arr1` and `arr2`.

Assume `arr1` has `x` zeros and `y` ones, while `arr2` has `a` zeros and `b` ones.

The number of AND results equal to 1 is `b*y`, and the number equal to 0 is `ax + ay + bx`.

When XORing afterward, we only need to judge the number of 1s, namely whether `b*y` is odd.

There is another C++ pitfall here. Because `arr.length` can be up to 10^5, `b*y` can overflow `int`. LeetCode enables overflow checks in the compiler, so it reports an error like the following. I got one penalty because of this.

The solution is either to use `long long`, or separately check `(b % 2 == 1) && (y % 2 == 1)`.

>Line 24: Char 26: runtime error: signed integer overflow: 100000 * 100000 cannot be represented in type 'int' (solution.cpp)
>SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior prog_joined.cpp:33:26

```cpp
class Solution {
    pair<int, int> extract(const vector<int>& arr, const int i) {
        int x = 0, y = 0;
        for (int num : arr) {
            if (num & (1 << i)) ++y;
            else ++x;
        }
        return {x, y};
    }
public:
    int getXORSum(vector<int>& arr1, vector<int>& arr2) {
        // brute-force: arr1.length * arr2.length = 10^10
        // smart: 32 * (arr1.length + arr2.length)
        // x0 y1
        // a0 b1
        // AND 0 = (ax + ay + bx)
        // AND 1 = (by)
        // if AND 1 % 2 == 1:
        //     k += (1 << i)
        int ans = 0;
        for (int i = 0; i < 31; ++i) {
            auto [x, y] = extract(arr1, i);
            auto [a, b] = extract(arr2, i);
            if ((b % 2 == 1) && (y % 2 == 1)) {
                ans += (1 << i);
            }
        }
        return ans;
    }
};
```
