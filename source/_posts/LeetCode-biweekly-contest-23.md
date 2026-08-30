---
title: LeetCode Biweekly Contest 23
date: 2020-04-07 13:08:11
description: A biweekly review of largest digit-sum groups, palindrome construction, circle-rectangle overlap and a sorted suffix-sum greedy choice of dishes.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/04/07/LeetCode-biweekly-contest-23/
  en: https://youngforest.github.io/en/2020/04/07/LeetCode-biweekly-contest-23/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-biweekly-contest-23/en-hero.webp" alt="Tokens fill equally tallest group cylinders, mirrored beads form centred necklaces, a compass measures circle-to-rectangle distance and sorted plates stop before the balance falls" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 199 / 7026 |	YoungForest | 19 | 	0:40:57 |  0:08:05 |  0:11:44 | 0:33:38 |   0:40:57 |

Not difficult; a speed contest. I found that in many speed contests, the third problem is sometimes even harder than the fourth.

## 1399. Count Largest Group

Straightforward. Use a HashMap to count `digit_sum -> count`, then find how many groups have the largest count.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    int countLargestGroup(int n) {
        unordered_map<int, int> seen;
        int largestSize = 0;
        for (int i = 1; i <= n; ++i)  {
            int sumOfDigit = 0;
            int current = i;
            while (current > 0) {
                sumOfDigit += current % 10;
                current /= 10;
            } 
            ++seen[sumOfDigit];
            largestSize = max(largestSize, seen[sumOfDigit]);
        }
        int ans = 0;
        for (auto it = seen.begin(); it != seen.end(); ++it) {
            if (it->second == largestSize)
                ++ans;
        }
        return ans;
    }
};
```

## 1400. Construct K Palindrome Strings

Count the number of characters that appear an odd number of times; it must be less than or equal to `k`. Also, since the required palindrome strings must be non-empty, we need `s.size() >= k`.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
public:
    bool canConstruct(string s, int k) {
        if (s.size() < k) return false;
        vector<int> count(26, 0);
        for (char c : s) {
            ++count[c - 'a'];
        }
        int single = 0;
        for (int i : count) {
            if (i % 2 == 1)
                ++single;
        }
        return single <= k;
    }
};
```

## 1401. Circle and Rectangle Overlapping

Based on the four sides of the square, divide the space into 9 parts, and classify the circle center into 9 possible positions. For each case, it is easy to determine whether it overlaps with the square.

Time complexity: O(1),
space complexity: O(1).

```cpp
class Solution {
    int distance(int x1, int y1, int x2, int y2) {
        return (x2 - x1) * (x2 - x1) + (y2 - y1) * (y2 -y1);
    }
public:
    bool checkOverlap(int radius, int x_center, int y_center, int x1, int y1, int x2, int y2) {
        auto closeEnough = [&](int x, int y) -> bool {
            return radius * radius >= distance(x_center, y_center, x, y);
        };
        auto inSquare = [&]() -> bool {
            return x_center <= x2 && x_center >= x1 && y_center <= y2 && y_center >= y1;
        };
        return closeEnough(x1, y1) || closeEnough(x1, y2) || closeEnough(x2, y2) || closeEnough(x2, y1) ||
            inSquare() || 
            (x_center <= x2 && x_center >= x1 && (y_center <= y2 + radius && y_center >= y1 - radius)) ||
            (y_center <= y2 && y_center >= y1 && (x_center <= x2 + radius && x_center >= x1 - radius)); 
    }
};
```

There is also a [simpler method to implement](https://leetcode.com/problems/circle-and-rectangle-overlapping/discuss/563441/JAVA-compare-distance-between-radius-and-closest-point-on-rectangle-to-circle) in the discussion section, which quickly finds the point in the rectangle closest to a given point, the circle center.

## 1402. Reducing Dishes

Greedy. Sort first, then take the dishes with the largest `satisfaction` values until the suffix sum becomes less than 0.

Time complexity: O(N log N),
space complexity: O(1).

```cpp
class Solution {
public:
    int maxSatisfaction(vector<int>& satisfaction) {
        const int n = satisfaction.size();
        sort(satisfaction.begin(), satisfaction.end());
        int currentSum = 0;
        int suffixSum = 0;
        for (int i = n - 1; i >= 0; --i) {
            suffixSum += satisfaction[i];
            if (suffixSum >= 0) {
                currentSum += suffixSum; 
            } else {
                return currentSum;
            }
        }
        return currentSum;
    }
};
```

My first submission used an `N^2` solution: sort and then enumerate the starting point. It actually passed.
