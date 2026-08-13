---
title: LeetCode Weekly Contest 235
date: 2021-04-05 12:12:49
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/04/05/LeetCode-weekly-contest-235/
  en: https://youngforest.github.io/en/2021/04/05/LeetCode-weekly-contest-235/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 889 / 11443 | YoungForest | 12 | 0:27:18 | 0:01:52 | 0:07:49 | 0:27:18 | null |

## 1816. Truncate Sentence

A warm-up problem. Let me emphasize again: string problems are suitable for Python. You really only need to describe the problem.

```python
class Solution:
    def truncateSentence(self, s: str, k: int) -> str:
        return ' '.join(s.split(' ')[:k])
```

Time complexity: O(s.length),
space complexity: O(s.length).

## 1817. Finding the Users Active Minutes

Brute force. Use ID as the key to count each user's action time sequence, then sort and deduplicate.

```cpp
class Solution {
public:
    vector<int> findingUsersActiveMinutes(vector<vector<int>>& logs, int k) {
        // brute-force:
        // logs.length + k
        unordered_map<int, vector<int>> actions; // userId -> actionMinute
        for (const auto& v : logs) {
            actions[v[0]].push_back(v[1]);
        }
        vector<int> ans(k, 0);
        for (auto& p : actions) {
            auto& v = p.second;
            sort(v.begin(), v.end());
            auto it = unique(v.begin(), v.end());
            const int d = distance(v.begin(), it);
            if (d >= 1 && d <= k) ++ans[d - 1];
        }
        return ans;
    }
};
```

Time complexity: O(logs.length * log(logs.length) + k),
space complexity: O(logs.length + k).

## 1818. Minimum Absolute Sum Difference

Greedy + Binary Search.

For each position, try replacing it to minimize the Diff. Use binary search to find the nearest optional value.

```cpp
class Solution {
    using ll = long long;
    ll MOD = 1e9 + 7;
public:
    int minAbsoluteSumDiff(vector<int>& nums1, vector<int>& nums2) {
        const int n = nums1.size();
        ll sumNums = 0;
        for (int i = 0; i < n; ++i) {
            sumNums += abs(nums1[i] - nums2[i]);
        }
        ll ans = sumNums;
        auto sortNums1 = nums1;
        sort(sortNums1.begin(), sortNums1.end());
        for (int i = 0; i < n; ++i) {
            auto it = lower_bound(sortNums1.begin(), sortNums1.end(), nums2[i]);
            if (it != sortNums1.end()) {
                ans = min(ans, sumNums - static_cast<ll>(abs(nums1[i] - nums2[i])) + static_cast<ll>(abs(*it - nums2[i])));
            }
            if (it != sortNums1.begin()) {
                it = prev(it);
                ans = min(ans, sumNums - static_cast<ll>(abs(nums1[i] - nums2[i])) + static_cast<ll>(abs(*it - nums2[i])));
            }
        }
        
        return ans % MOD;
    }
};
```

Time complexity: O(nums.length * log nums.length),
space complexity: O(nums.length).

## 1819. Number of Different Subsequences GCDs

During the contest I TLE'd and did not come up with an efficient algorithm.

Brute-force DP, maintaining a Set of GCDs of all subsequences of the current array.

Time complexity: O(N ^ 2), N = nums.length,
space complexity: O(max(nums[i])). The number of GCDs can be at most this many.

```cpp
class Solution {
public:
    int countDifferentSubsequenceGCDs(vector<int>& nums) {
        unordered_set<int> dp;
        for (int x : nums) {
            unordered_set<int> newDp;
            newDp.insert(x);
            for (int a : dp) {
                newDp.insert(__gcd(a, x));
            }
            for (int x : newDp) {
                dp.insert(x);
            }
        }
        return dp.size();
    }
};
```

Other similar GCD problems, for anyone interested:

- https://atcoder.jp/contests/abc191/tasks/abc191_f
