---
title: LeetCode Weekly Contest 222
date: 2021-01-03 16:07:11
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 331 / 9692 | YoungForest | 18 | 2:02:29 | 0:05:32	 | 0:13:55  2 | 0:54:53  2 | 1:17:29  5 |

## 5641. Maximum Units on a Truck

Greedy. Sort boxes by capacity from large to small, then use the larger boxes first.

```cpp
class Solution {
public:
    int maximumUnits(vector<vector<int>>& boxTypes, int truckSize) {
        // greedy, put larger boxes first
        sort(boxTypes.begin(), boxTypes.end(), [](const auto& a, const auto& b) -> bool {
            if (a[1] != b[1]) return a[1] > b[1];
            else return a[0] > b[0];
        });
        int ans = 0;
        int i = 0;
        while (truckSize > 0 && i < boxTypes.size()) {
            const int put = min(truckSize, boxTypes[i][0]);
            truckSize -= put;
            ans += boxTypes[i][1] * put;
            ++i;
        }
        return ans;
    }
};
```

Time complexity: O(n log n), `n = boxTypes.size()`
space complexity: O(log n), from quicksort's internal recursion cost.

## 5642. Count Good Meals

Similar to `two-sum`. Use a hashtable to maintain previously seen numbers, except the number of targets becomes 22.
From `2^0` to the largest `2^21`.

```cpp
class Solution {
    using ll = int;
    const int maxBit = 22;
    const int MOD = 1e9 + 7;
public:
    int countPairs(vector<int>& deliciousness) {
        vector<ll> target(maxBit);
        target[0] = 1;
        for (int i = 1; i < maxBit; ++i) {
            target[i] = target[i-1] * 2;
        }
        unordered_map<ll, int> count;
        int ans = 0;
        for (ll i : deliciousness) {
            for (int j = 0; j < maxBit; ++j) {
                auto it = count.find(target[j] - i);
                if (it != count.end()) {
                    ans = (ans + it->second) % MOD;
                }
            }
            ++count[i];
        }
        return ans;
    }
};
```

Time complexity: O(22n),
space complexity: O(n).

During the contest, I misestimated the maximum power. `2^20 + 2^20 = 2^21`, but I mistakenly estimated it as `2^40`. That caused two TLE penalties.

## 5643. Ways to Split Array Into Three Subarrays

Enumerate each left subarray. Then use prefix sums and binary search to determine the size of the middle subarray.

```cpp
class Solution {
    const int MOD = 1e9 + 7;
public:
    int waysToSplit(vector<int>& nums) {
        const int n = nums.size();
        vector<int> presum(n + 1);
        presum[0] = 0;
        for (int i = 0; i < n; ++i) {
            presum[i+1] = presum[i] + nums[i];
        }
        auto binarySearchRightLessThanMid = [&](int lo, int hi) -> int {
            // [lo, hi)
            const int begin = lo;
            while (lo < hi) {
                // [begin, mid), [mid, end)
                const int mid = lo + (hi - lo) / 2;
                const int rightSum = presum[n] - presum[mid];
                const int midSum = presum[mid] - presum[begin];
                if (rightSum < midSum) {
                    hi = mid;
                } else { // rightSum >= midSum
                    lo = mid + 1;
                }
            }
            return lo;
        };
        auto binarySearchFirstLargeEqualThan = [&](int lo, int hi, const int leftSum) -> int {
            // [lo, hi)
            const int begin = lo;
            while (lo < hi) {
                // [begin, mid)
                const int mid = lo + (hi - lo) / 2;
                const int midSum = presum[mid] - presum[begin];
                if (midSum >= leftSum) {
                    hi = mid;
                } else { // rightSum < midSum
                    lo = mid + 1;
                }
            }
            return lo;
        };
        int ans = 0;
        for (int left = 1; left <= n - 2; ++left) {
            // [0, left)
            const int leftSum = presum[left];
            // [left, mid)
            int midLeft = binarySearchFirstLargeEqualThan(left, n, leftSum);
            if (midLeft == left) midLeft = left + 1;
            const int midRight = binarySearchRightLessThanMid(left, n);
            // cout << left << " " << leftSum << " " << midLeft << " " << midRight << endl;
            if (midRight > midLeft)
                ans = (ans + (midRight - midLeft)) % MOD;
        }
        return ans;
    }
};
```

Time complexity: O(n log n),
space complexity: O(n).

During the contest, I got 2 WAs because of boundary issues, namely corner cases.

- When the left array sum is 0, the middle array still needs to be non-empty.
- When the whole array is all 9s, the right array must be non-empty.

In fact, since `midRight` and `midleft` are monotonically increasing, a [three-pointer method](https://leetcode-cn.com/problems/ways-to-split-array-into-three-subarrays/solution/cong-shuang-zhi-zhen-dao-san-zhi-zhen-by-klrb/) can further reduce the time complexity to O(n).

## 5644. Minimum Operations to Make a Subsequence

Everyone learned and copied this one live during the contest. The reason I say that is because this problem can be transformed into another classic problem.

First, observe that numbers in `arr` that do not appear in `target` are useless and can be deleted directly.
After deletion, `arr` is a permutation of `target` (not exactly, because some numbers may be duplicated in `arr`).
We only need to find the length of the longest common subsequence (Longest Common Subsequence, LCS) of the two, then fill in the rest.
However, LCS has time complexity O(mn), which clearly times out.
At this point, we need to use the permutation condition. I Googled `permutation longest common subsequence`, and the third result had the solution: [Longest Common Subsequence and Its Variants](https://www.cnblogs.com/Jackpei/p/10356999.html).
Specifically, for this special LCS case involving a permutation, it can be transformed into a Longest Increasing Subsequence (LIS) problem. LIS also has a clever O(n log n) solution.


```cpp
class Solution {
    int nums[100005];
    int lengthOfLIS(int n) {
        vector<int> res;
        for(int i=0; i<n; i++) {
            auto it = std::lower_bound(res.begin(), res.end(), nums[i]);
            if(it==res.end()) res.push_back(nums[i]);
            else *it = nums[i];
        }
        return res.size();
    }
public:
    int minOperations(vector<int>& target, vector<int>& arr) {
        // n log n
        int cnt = 0;
        unordered_map<int, int> m;
        for (int i : target) {
            m[i] = cnt;
            ++cnt;
        }
        int x = 0;
        for (int i : arr) {
            auto it = m.find(i);
            if (it != m.end()) {
                nums[x++] = it->second;
            }
        }
        int lcs = lengthOfLIS(x);
        return target.size() - lcs;
    }
};
```

Time complexity: O(n log n), `n = target.size()`
space complexity: O(target.size()).

Because I copied the LIS template wrong, I got TLE 3 times. Because I initially used the naive O(mn) LCS solution, I got TLE 2 more times.

## Postscript

Today was the first weekly contest of 2021, and it was a bit harder than usual.
Winter in Beijing is truly too cold. Getting up has become extremely difficult, my enthusiasm for life is also quite lacking, and my state is a little negative and sad.
I want to cheer up and become positive again, and begin as a striving Forest.
Worker soul, worker spirit; working is what makes one rise above others.
Come on, Forest!
