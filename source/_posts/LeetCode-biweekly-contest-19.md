---
title: LeetCode biweekly contest 19
date: 2020-02-10 11:30:20
description: A biweekly review of reduction to zero, sliding windows, clock angles and same-value jump paths, plus competing from home while rusty.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/02/10/LeetCode-biweekly-contest-19/
  en: https://youngforest.github.io/en/2020/02/10/LeetCode-biweekly-contest-19/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-biweekly-contest-19/en-hero.webp" alt="Forest watches a halving staircase, grocery sliding scale, blank clock hands and a runner using coloured island portals" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 179 / 3745 |	YoungForest | 18 | 0:41:10 | 0:02:39 | 0:10:36 |  0:12:53  | 0:36:10 1 |

This was my first biweekly contest after returning to China. My hands were a bit rusty, and my form was still recovering. Recently, because of the novel coronavirus epidemic, I have been isolating at home and almost cannot go out except to buy groceries. The New Year atmosphere disappeared this year because of it. Staying at home almost made me go stir-crazy. The contest result was okay. Speed contests have also always been a type I am not good at.

## 1342. Number of Steps to Reduce a Number to Zero

A warm-up problem. Just simulate the two operations: divide by 2 and subtract one.

Time complexity: O(log N),
space complexity: O(1).

```cpp
class Solution {
public:
    int numberOfSteps (int num) {
        int step = 0;
        while (num > 0) {
            if (num % 2 == 0) {
                num /= 2;
            } else {
                --num;
            }
            ++step;
        }
        return step;
    }
};
```

## 1343. Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold

A simple sliding window. We can directly apply the template.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
public:
    int numOfSubarrays(vector<int>& arr, int k, int threshold) {
        int ans = 0;
        int s = 0;
        for (int i = 0; i < k; ++i) {
            s += arr[i];
        }
        if (s >= threshold * k) {
            ++ans;
        }
        for (int i = k; i < arr.size(); ++i) {
            s = s + arr[i] - arr[i - k];
            if (s >= threshold * k) {
                ++ans;
            }
        }
        
        return ans;
    }
};
```

## 1344. Angle Between Hands of a Clock

Compute the angle between the hour hand and the minute hand on a clock. Calculate their positions separately, then compute the included angle.

Time complexity: O(1),
space complexity: O(1).

```cpp
class Solution {
public:
    double angleClock(int hour, int minutes) {
        double h = hour;
        double m = minutes;
        h = h * 5 + m / 12;
        double x = abs(h - m) * 6;
        if (x >= 180) {
            return 360 - x;
        } else {
            return x;
        }
    }
};
```

## 1345. Jump Game IV

Another Jump Game problem. I originally thought it would use DP like the previous ones. After one attempt, I found that it actually needed BFS.
One thing to note is that after using the reverse array each time, we need to clear the array so that duplicated computation will not happen. Thanks to [hiepit's post](https://leetcode.com/problems/jump-game-iv/discuss/502699/JavaC%2B%2B-BFS-Solution-Clean-code-O(N)).

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
    const int INF = 0x3f3f3f3f;
public:
    int minJumps(vector<int>& arr) {
        if (arr.size() <= 1) return 0;
        int target = arr.size() - 1;
        unordered_map<int, vector<int>> reverse;
        for (int i = arr.size() - 1; i >= 0; --i) {
            reverse[arr[i]].push_back(i);
        }
        queue<int> q;
        q.push(0);
        unordered_set<int> seen;
        seen.insert(0);
        int step = 1;
        while (!q.empty()) {
            int s = q.size();
            for (int i = 0; i < s; ++i) {
                int index = q.front();
                q.pop();
                auto& condidates = reverse[arr[index]];
                condidates.push_back(index + 1);
                condidates.push_back(index - 1);
                for (int next : condidates) {
                    if (next == target)
                        return step;
                    if (next < arr.size() && next >= 0 && seen.find(next) == seen.end()) {
                        seen.insert(next);
                        q.push(next);
                    }
                }
                condidates.clear(); // important, avoid duplicated computing
            }
            ++step;
        }
        return -1;
    }
};
```
