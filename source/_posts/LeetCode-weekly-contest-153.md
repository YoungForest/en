---
title: LeetCode weekly contest 153
date: 2019-09-15 20:31:56
description: Weekly notes on circular routes, weekdays, one-deletion maximum sums and replacements for a strictly increasing array before moving abroad.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/09/15/LeetCode-weekly-contest-153/
  en: https://youngforest.github.io/en/2019/09/15/LeetCode-weekly-contest-153/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-153/en-hero.webp" alt="Forest waits with a blank suitcase among a circular shuttle route, seven-light calendar wheel, one-bead removal track and replacement staircase" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 392 / 6212 |	YoungForest | 	12	 | 	0:41:42 | 0:06:46  1 | 0:16:11 | 0:36:42 | null |

This was my last contest while still in China. Because of the time difference in Belgium, the weekly contest there is from 4:30 a.m. to 6:00 a.m. every Sunday. So I do not really have the conditions to participate, and can only get up on Sunday mornings to solve the problems afterward.

## 1184. Distance Between Bus Stops

Two pass. Walk in the forward direction once, walk the full circle once, then subtract the forward distance from the total distance to get the reverse distance.
Here, note that `start` must be before `destination`; otherwise, swap their positions.

```cpp
class Solution {
public:
    int distanceBetweenBusStops(vector<int>& distance, int start, int destination) {
        int total = accumulate(distance.begin(), distance.end(), 0);
        if (start > destination)
            swap(start, destination);
        int clockwise = accumulate(distance.begin() + start, distance.begin() + destination, 0);
        int counterclockwise = total - clockwise;
        return min(clockwise, counterclockwise);
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1185. Day of the Week

A classic calendar problem. The point to pay attention to is handling leap years.

Time complexity: O(year * month),
space complexity: O(1).

```cpp
class Solution {
    bool isLeap(int year) {
        return (year % 4 == 0 && year % 100 != 0) || (year % 400 == 0);
    }
public:
    string dayOfTheWeek(int day, int month, int year) {
        vector<string> v = {"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"};
        vector<int> monthes = {0, 31, 28, 31, 30, 31,30, 31, 31, 30,31,30,31};
        int count = 4;
        for (int i = 1971; i < year; ++i) {
            if (isLeap(i)) {
                count += 366;
            } else {
                count += 365;
            }
        }
        if (isLeap(year)) {
            monthes[2] += 1;
        }
        count += accumulate(monthes.begin(), monthes.begin() + month, 0);
        count += day;
        count %= 7;
        return v[count];
    }
};
```

## 1186. Maximum Subarray Sum with One Deletion

Dynamic Programming (DP).
The result at position i can be obtained from the result at position i - 1.
Because at most one element can be deleted, DP needs to maintain two minimum values: one for the state where one element has been deleted, and another for the state where none has been deleted. It also needs to maintain the minimum value that can be deleted.

```cpp
class Solution {
public:
    int maximumSum(vector<int>& arr) {
        const int n = arr.size();
        vector<int> dp_delete(n + 1, 0), dp_nodelete(n + 1, 0), dp_which_min(n + 1, 0);
        int ret = arr[0];
        for (int i = 1; i < n + 1; ++i) {
            if (dp_nodelete[i - 1] + arr[i - 1] >= arr[i - 1]) {
                dp_which_min[i] = min(dp_which_min[i - 1], arr[i - 1]);
            } else {
                dp_which_min[i] = arr[i - 1];
            }
            dp_nodelete[i] = max(dp_nodelete[i - 1] + arr[i - 1], arr[i - 1]);
            dp_delete[i] = max(dp_delete[i - 1] + arr[i - 1], dp_nodelete[i - 1] + arr[i - 1] - dp_which_min[i - 1]);
            ret = max({ret, dp_nodelete[i], dp_delete[i]});
        }
        return ret;
    }
};
```

Time complexity: O(N),
space complexity: O(N). It can be optimized to O(1).

## 1187. Make Array Strictly Increasing

A relatively brute-force method: DFS + memorization.
When searching the next position, there are two choices: replace or do not replace. Use pruning and memorization to speed it up.
We can find that in the worst case, the search space is only n * m because of memo.

Time complexity: O(n * m * log m),
space complexity: O(n * m)

```cpp
class Solution {
    const int FAIL = 3000;
    unordered_map<int, unordered_map<int, int>> memo;
    int dfs(vector<int>& arr1, vector<int>& arr2, int i, int prev) {
        if (i == arr1.size()) {
            return 0;
        }
        if (memo.find(i) != memo.end() && memo[i].find(prev) != memo[i].end()) {
            return memo[i][prev];
        }
        // pickarr2
        auto it = upper_bound(arr2.begin(), arr2.end(), prev);
        if (arr1[i] > prev) {
            if (it != arr2.end() && *it < arr1[i])
                return memo[i][prev] = min(dfs(arr1, arr2, i + 1, arr1[i]), dfs(arr1, arr2, i + 1, *it) + 1);
            else
                return memo[i][prev] = dfs(arr1, arr2, i + 1, arr1[i]);
        } else {
            if (it == arr2.end()) {
                return memo[i][prev] = FAIL;
            } else {
                return memo[i][prev] = dfs(arr1, arr2, i + 1, *it) + 1;
            }
        }
    }
public:
    int makeArrayIncreasing(vector<int>& arr1, vector<int>& arr2) {
        sort(arr2.begin(), arr2.end());
        auto ans = dfs(arr1, arr2, 0, -1);
        if (ans >= FAIL) {
            return -1;
        } else {
            return ans;
        }
    }
};
```
