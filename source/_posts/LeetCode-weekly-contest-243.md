---
title: LeetCode Weekly Contest 243
date: 2021-05-30 17:22:10
description: "Four solutions convert words to values, insert one digit greedily, schedule servers with priority queues, and use DP to minimize skipped rests."
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/05/30/LeetCode-weekly-contest-243/
  en: https://youngforest.github.io/en/2021/05/30/LeetCode-weekly-contest-243/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 95 / 12835 | YoungForest | 18 | 1:19:18 | 0:02:50 | 0:11:21 | 0:36:27  🐞1 | 1:04:18 🐞2 |

LingShen's big data:

1880,Check if Word Equals Summation of Two Words,check-if-word-equals-summation-of-two-words,1187.1641565458
1881,Maximum Value after Insertion,maximum-value-after-insertion,1381.2168789318
1882,Process Tasks Using Servers,process-tasks-using-servers,1979.1112273597
1883,Minimum Skips to Arrive at Meeting On Time,minimum-skips-to-arrive-at-meeting-on-time,2587.8725248485

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-243/en-hero.webp" alt="Coloured bead strings balance on an abacus, one bead enters the best slot, twin queues dispatch tasks and a road skips selected rests before the timer empties" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 1880. Check if Word Equals Summation of Two Words

A warm-up problem. Convert strings to numbers according to the problem statement, then judge.

```python
class Solution:
    def isSumEqual(self, firstWord: str, secondWord: str, targetWord: str) -> bool:
        def toDigit(w):
            ans = 0
            for i in w:
                ans = ans * 10 + ord(i) - ord('a')
            return ans
        
        return toDigit(firstWord) + toDigit(secondWord) == toDigit(targetWord)
```

Time complexity: O(N),
space complexity: O(N).

## 1881. Maximum Value after Insertion

Greedy. For a positive number, insert before the first digit smaller than `x`; for a negative number, insert before the first digit greater than `x`.

```python
class Solution:
    def maxValue(self, n: str, x: int) -> str:
        # insert into the position where the first digit < x
        # negative first > x
        if n[0] == '-':
            for i in range(1, len(n)):
                if ord(n[i]) - ord('0') > x:
                    return n[:i] + str(x) + n[i:]
            return n + str(x)
        else:
            for i in range(len(n)):
                if ord(n[i]) - ord('0') < x:
                    return n[:i] + str(x) + n[i:]
            return n + str(x)
```

Time complexity: O(N),
space complexity: O(N).

## 1882. Process Tasks Using Servers

Direct brute-force simulation is enough. Use priority queues to maintain idle servers, waiting tasks, and release times.

During simulation, note that time cannot move one unit at a time. It should only move to moments when events happen: tasks begin waiting or servers are released.

Also note that the time value may exceed `int`; using `long long` is safer.

```cpp
class Solution {
    using ll = long long;
    using pii = pair<ll, ll>;
public:
    vector<int> assignTasks(vector<int>& servers, vector<int>& tasks) {
        // brute-force: (m + n) * log n
        priority_queue<pii, vector<pii>, greater<>> freeServers, releaseTime;
        for (int i = 0; i < servers.size(); ++i) {
            freeServers.emplace(servers[i], i);
        }
        priority_queue<int, vector<int>, greater<>> waitTasks;
        const int m = tasks.size();
        vector<int> ans(m);
        for (int i = 0; i < m; ++i) {
            waitTasks.push(i);
            while (!releaseTime.empty() && releaseTime.top().first <= i) {
                const int idx = releaseTime.top().second;
                freeServers.emplace(servers[idx], idx);
                releaseTime.pop();
            }
            while (!freeServers.empty() && !waitTasks.empty()) {
                const int idx = waitTasks.top();
                waitTasks.pop();
                auto [weight, serverIdx] = freeServers.top();
                freeServers.pop();
                ans[idx] = serverIdx;
                releaseTime.emplace(i + tasks[idx], serverIdx);
            }
        }
        ll current = m - 1;
        while (!releaseTime.empty() && !waitTasks.empty()) {
            const int idx = releaseTime.top().second;
            current = releaseTime.top().first;
            freeServers.emplace(servers[idx], idx);
            releaseTime.pop();
            while (!releaseTime.empty() && releaseTime.top().first <= current) {
                const int idx = releaseTime.top().second;
                current = releaseTime.top().first;
                freeServers.emplace(servers[idx], idx);
                releaseTime.pop();
            }
            while (!freeServers.empty() && !waitTasks.empty()) {
                const int idx = waitTasks.top();
                waitTasks.pop();
                auto [weight, serverIdx] = freeServers.top();
                freeServers.pop();
                ans[idx] = serverIdx;
                releaseTime.emplace(current + tasks[idx], serverIdx);
            }
        }
        
        return ans;
    }
};
```

Time complexity: O((m + n) * log n),
space complexity: O(m + n).

## 1883. Minimum Skips to Arrive at Meeting On Time

Dynamic Programming.

`dp(i, k)` represents the earliest ending time from `dist0` to `i` inclusive, with `k` rests.

The state transition is:

dp(i, k) = min(
    dp(i-1, k) + .. no rest at the last stop
    dp(i-1, k-1) + .. rest at the last stop
),

Then use binary search to find the minimum `k` such that `dp(n-1, k) <= hoursBefore`.

Actually, binary search is not necessary; scanning from small to large would also work, because the time-complexity bottleneck is not there, but in computing `dp`.

Recently I have often encountered Dynamic Programming problems. Previously, when finding the state transition, my time complexity was always too high because I tried to enumerate where the last rest position occurred. The correct approach should be to enumerate only whether the last stop rests. This reduces time complexity by one factor of `n`.

This problem has a floating-point precision pitfall, and I got WA twice because of it.

There are two solutions:

1. Add an `err`; I used 10^-9 here, which is enough in most cases.
2. Convert to integers. This may require `long long`. In this problem, represent all `time * speed` as distance, which avoids precision loss.

```python
class Solution:
    def minSkips(self, dist: List[int], speed: int, hoursBefore: int) -> int:
        n = len(dist)
        err = 10**-9
        @cache
        def dp(i, k):
            if i == 0:
                return dist[0] / speed
            if k == 0:
                # no relax
                return ceil(dp(i-1, 0) - err) + dist[i] / speed
            if i == k:
                # relax all
                return dp(i-1, k-1) + dist[i] / speed
            # not relax + relax
            return min(ceil(dp(i-1, k) - err) + dist[i] / speed, dp(i-1, k-1) + dist[i] / speed)
                       
        lo = 0
        hi = n
        
        if dp(n-1, n-1) > hoursBefore + err: return -1
        
        while lo < hi:
            mid = lo + (hi - lo) // 2
            if dp(n-1, mid) <= hoursBefore + err: # -err?
                hi = mid
            else:
                lo = mid + 1
        
        if lo == n: return -1
        else: return lo
```

Time complexity: O(n ^ 2),
space complexity: O(m ^ 2).
