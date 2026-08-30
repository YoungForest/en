---
title: LeetCode weekly contest 216
date: 2020-11-22 17:32:56
description: Contest solutions concatenate string arrays, greedily build a minimal string, balance parity sums, and order tasks to minimize initial energy.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/11/22/LeetCode-weekly-contest-216/
  en: https://youngforest.github.io/en/2020/11/22/LeetCode-weekly-contest-216/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-216/en-hero.webp" alt="Differently cut strips form the same ribbon, bead slots fill from the far end, parity trays rebalance after one removal and task batteries line up by their energy gaps" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 862 / 9573 | YoungForest | 18 | 0:58:34 | 0:12:47	 | 0:23:33 | 0:33:14 | 0:58:34 |

Previously I had been exempt from checking in with the Cruel Problem Solving group for five consecutive weeks, which felt great for a whole month. Last week, although I ranked within the top 500, I only solved three problems. This week I solved four problems, but fell outside the top 500. Now I have to check in for another week. My ranking in the Cruel group also dropped to 38, no longer the peak days of rank 15.

Since full-time recruiting ended, and because my internship and thesis have been especially busy, I have had no time to practice problems. Even during the busiest period before submitting my thesis format review, I did not solve a single problem. Since November, I have resumed the habit of doing the daily problem on both the Chinese and US LeetCode sites. Mainly because these two problems are usually relatively easy and take little time. Another reason is that I want to check in and earn points, trying to exchange for two sets of clothes before graduation.

The difficulty of the three check-in problems is roughly: `Cruel > Chinese site >= US site`.

## 1662. Check If Two String Arrays are Equivalent

A warm-up problem. Concatenate the list of strings and then compare.

```cpp
class Solution {
    string concat(vector<string>& w) {
        string ans;
        for (auto& s : w) {
            ans += move(s);
        }
        return ans;
    }
public:
    bool arrayStringsAreEqual(vector<string>& word1, vector<string>& word2) {
        return concat(word1) == concat(word2);
    }
};
```

Time complexity: O(sum(word1[i].length) + sum(word2[i].length)),
Space complexity: O(sum(word1[i].length) + sum(word2[i].length)).

## 1663. Smallest String With A Given Numeric Value

Greedy. Increase the last character as much as possible each time.

```cpp
class Solution {
public:
    string getSmallestString(int n, int k) {
        string ans(n, 'a');
        k -= n;
        for (int i = n - 1; i >= 0 && k > 0; --i) {
            const int thisDigit = min(k, 25);
            ans[i] += thisDigit;
            k -= thisDigit;
        }
        return ans;
    }
};
```

Time complexity: O(N),
Space complexity: O(N).

## 1664. Ways to Make a Fair Array

Use prefix even sums and prefix odd sums, similar to prefix sum arrays. Also use suffix even sums and suffix odd sums.

Then we can quickly calculate the sum of odd-indexed elements and even-indexed elements after deleting a certain position.

```cpp
class Solution {
public:
    int waysToMakeFair(vector<int>& nums) {
        const int n = nums.size();
        int ans = 0;
        vector<int> leftOdd(n, 0);
        vector<int> leftEven(n, 0);
        vector<int> rightOdd(n, 0);
        vector<int> rightEven(n, 0);
        leftEven[0] = nums[0];
        for (int i = 1; i < nums.size(); ++i) {
            if (i % 2 == 1) {
                leftOdd[i] = leftOdd[i - 1] + nums[i];
                leftEven[i] = leftEven[i - 1];
            } else {
                leftEven[i] = leftEven[i - 1] + nums[i];
                leftOdd[i] = leftOdd[i - 1];
            }
        }
        if ((n - 1) % 2 == 0) {
            rightEven[n-1] = nums[n-1];
        } else {
            rightOdd[n-1] = nums[n-1];
        }
        for (int i = n - 2; i >= 0; --i) {
            if (i % 2 == 1) {
                rightOdd[i] = rightOdd[i + 1] + nums[i];
                rightEven[i] = rightEven[i + 1];
            } else {
                rightEven[i] = rightEven[i + 1] + nums[i];
                rightOdd[i] = rightOdd[i + 1];
            }
        }
        for (int i = 0; i < nums.size(); ++i) {
            int odd = 0, even = 0;
            odd += i > 0 ? leftOdd[i - 1] : 0;
            even += i > 0 ? leftEven[i - 1] : 0;
            odd += i < n - 1 ? rightEven[i + 1] : 0;
            even += i < n - 1 ? rightOdd[i + 1] : 0;
            if (odd == even) ++ans;
        }
        return ans;
    }
};
```

Time complexity: O(N),
Space complexity: O(N).

## 1665. Minimum Initial Energy to Finish Tasks

See [LingShen's post](https://leetcode-cn.com/problems/minimum-initial-energy-to-finish-tasks/solution/wan-cheng-suo-you-ren-wu-de-zui-shao-chu-shi-neng-/).

During the contest, I used an incorrect greedy approach and still passed: find the minimum difference `|minimum_i - actual_i|` as the remaining value, then compare this value with the largest `minimum_i`.

The correct solution is really too hard for a 6-point problem. It should be 7 or 8 points. Many people, even if they guessed the right idea, could not prove its correctness.

```cpp

class Solution {
public:
    int minimumEffort(vector<vector<int>>& tasks) {
        sort(tasks.begin(), tasks.end(), [](const auto& u, const auto& v) {
            return u[0] - u[1] < v[0] - v[1];
        });
        int p = 0;
        int suma = 0;
        for (const auto& task: tasks) {
            p = max(p, suma + task[1]);
            suma += task[0];
        }
        return p;
    }
};
```

Time complexity: O(N log N),
Space complexity: O(1).
