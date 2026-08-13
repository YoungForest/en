---
title: LeetCode Weekly Contest 230
date: 2021-03-01 09:51:47
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/03/01/LeetCode-weekly-contest-230/
  en: https://youngforest.github.io/en/2021/03/01/LeetCode-weekly-contest-230/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 314 / 11654 | YoungForest | 12 | 	0:27:36 |  0:04:00 |  0:14:38 | 0:27:36 | null |

Since autumn recruitment ended, my enthusiasm for practicing problems and contests has declined day by day.
Previously I did three problems every day, China server, US server, and Cruel. Now I do zero problems a day. Of course, occasionally, when my weekly contest result is not enough to exempt me from check-in, I still need to do one problem per day.
In contrast, contest feedback is still quite strong. In the long term, rating and ranking growth provide motivation. In the short term, Cruel ranking and each contest ranking provide motivation, plus extra points as rewards. Also, after the weekly contest, I can get a red packet in the Cruel group and add a chicken drumstick for myself.

## 1773. Count Items Matching a Rule

Warm-up problem. Just traverse once according to the problem statement.

```cpp
class Solution {
public:
    int countMatches(vector<vector<string>>& items, string ruleKey, string ruleValue) {
        int index = -1;
        if (ruleKey == "type") {
            index = 0;
        } else if (ruleKey == "color") {
            index = 1;
        } else {
            index = 2;
        }
        int ans = 0;
        for (const auto& v : items) {
            if (v[index] == ruleValue) {
                ++ans;
            }
        }
        return ans;
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1774. Closest Dessert Cost

Brute force enumeration is enough. Looking at the constraints, both `n` and `m` are small, so exponential brute-force search can pass.
You can use only `backtracking`, or use a ternary `bitmask` to enumerate.
During the contest I used backtracking.

```cpp
class Solution {
    const int INF = 0x3f3f3f3f;
public:
    int closestCost(vector<int>& baseCosts, vector<int>& toppingCosts, int target) {
        // time: n * (3 ^ m)
        // 10 * 3 ^ 10 59049
        // max: 10^4 + 2 * 10 * 10^4
        vector<int> candidates;
        const int m = toppingCosts.size();
        function<void(const int, const int)> backtracking = [&](const int i, const int now) -> void {
            if (i == m) {
                candidates.push_back(now);
            } else {
                for (int j = 0; j <= 2; ++j) {
                    backtracking(i + 1, now + toppingCosts[i] * j);
                }
            }
        };
        for (int base : baseCosts) {
            backtracking(0, base);
        }
        int ans = -1, diff = INF;
        for (int i : candidates) {
            const int diffI = abs(i - target);
            if (diffI < diff) {
                ans = i;
                diff = diffI;
            } else if (diffI == diff && i < ans) {
                ans = i;
            }
        }
        return ans;
    }
};
```

Time complexity: O(n * 3 ^ m), 
space complexity: O(n * 3 ^ m).

## 1775. Equal Sum Arrays With Minimum Number of Operations

Greedy. Prioritize the operation with the largest possible change.

```cpp
class Solution {
    struct Content {
        int sumOfAll = 0;
        vector<int> nums;
        Content() {
            nums.resize(7, 0);
        }
    };
    Content preprocess(const vector<int>& arr) {
        Content ans;
        for (int i : arr) {
            ans.sumOfAll += i;
            ++ans.nums[i];
        }
        return ans;
    }
public:
    int minOperations(vector<int>& nums1, vector<int>& nums2) {
        // 1 -> 6 6 -> 1 5
        // 2 -> 6 5 -> 1 4
        // 3
        auto c1 = preprocess(nums1);
        auto c2 = preprocess(nums2);
        if (c1.sumOfAll > c2.sumOfAll) {
            swap(c1, c2);
        }
        // c1 < c2
        int ans = 0;
        for (int i = 1; i <= 5; ++i) {
            const int j = 7 - i;
            const int cost = 6 - i;
            const int need = c2.sumOfAll - c1.sumOfAll;
            const int needStep = need / cost + ((need % cost == 0) ? 0 : 1);
            // 3 2 -> 2
            // 4 2 -> 2
            // 2 2 -> 1
            if (needStep <= c1.nums[i] + c2.nums[j]) {
                return ans + needStep;
            }
            c1.sumOfAll += c1.nums[i] * cost;
            c2.sumOfAll -= c2.nums[j] * cost;
            ans += c1.nums[i] + c2.nums[j];
        }
        return -1;
    }
};
```

Time complexity: O(nums1.size() + nums2.size()),
space complexity: O(1).

## 1776. Car Fleet II

A relatively difficult problem. I did not solve it during the contest, but I had some ideas. We can first determine how many fleets the cars will eventually split into. From back to front, by checking whether a car can catch up, `not meeting: speed[i] > min of speed [i+1:]`, the problem can first be divided into several subproblems. Then within each subproblem, maintain a priority queue of collision times to update car states and new collision events. The time complexity is O(N log N).
Later, this idea proved too cumbersome to implement and too error-prone.

Although using a priority queue to record collision events can work, the implementation, especially updating collisions, is very cumbersome.
The elegant solution is a monotonic stack. For details, refer to [the group owner's video](https://www.bilibili.com/video/BV1DK4y1S743/).
