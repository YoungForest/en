---
title: LeetCode Weekly Contest 255
date: 2021-08-23 11:39:46
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (6) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 406 / 11837 | YoungForest | 	12 | 	0:36:07 | 0:01:21 | 0:09:54 |  0:26:07  🐞2 | null |

This week's Q4 was extremely hard. After thinking for half an hour, I decisively gave up and went to make Coca-Cola chicken wings with my girlfriend.

Interestingly, after the contest, LC China went down. Scores could not be fetched, and the Cruel Coding board defaulted everyone to 0 points. I felt bad for the students on the China site.

Although it recovered afterward, the rank shown above is the rank after recovery, with China-site participants included.

Because there was actually a Google Kick Start contest today, the number of participants was somewhat lower.

## 1979. Find Greatest Common Divisor of Array

A warm-up problem. Using C++ here is basically cheating, because it has a built-in `gcd` function. Although it is only supported starting from C++17, LeetCode happens to support C++17.

```cpp
class Solution {
public:
    int findGCD(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        const int smallest = *nums.begin();
        const int largest = *nums.rbegin();
        return gcd(largest, smallest);
    }
};
```

Time complexity: O(n log n + log max(nums)),
space complexity: O(1).

Time complexity of gcd: [link](https://www.quora.com/What-is-the-time-complexity-of-Euclids-GCD-algorithm).

## 1980. Find Unique Binary String

Since `n` is very small, brute force is enough.

Converting a number to a binary string and converting a binary string to a number were both searched online on the spot.

```cpp
class Solution {
public:
    string findDifferentBinaryString(vector<string>& nums) {
        // total number: 2 ^ n,
        // 2 ^ 16 = 65536
        const int n = nums.size();
        unordered_set<int> seen;
        for (const auto& i : nums) {
            seen.insert(stoi(i, 0, 2));
        }
        for (int i = 0; i < (1 << n); ++i) {
            if (seen.find(i) == seen.end()) {
                return std::bitset<16>(i).to_string().substr(16 - n);
            }
        }
        return "";
    }
};
```

Time complexity: O(sum(nums[i].length) + 2 ^ n),
space complexity: O(n).

## 1981. Minimize the Difference Between Target and Chosen Elements

Use DP to enumerate all possible sums, then find the closest one.

```cpp
class Solution {
public:
    int minimizeTheDifference(vector<vector<int>>& mat, int target) {
        // 1 * m ~ 70 * m = 4900
        // 4900 * n * m = 24,010,000
        const int MAXNUMBER = 70;
        const int m = mat.size();
        vector<bool> dp(MAXNUMBER * m + 1, false);
        dp[0] = true;
        for (int i = 0; i < m; ++i) {
            vector<bool> newdp(MAXNUMBER * m + 1, false);
            for (int j = MAXNUMBER * m; j >= 0; --j) {
                if (dp[j]) {
                    for (int k : mat[i]) {
                        if (j + k <= MAXNUMBER * m) newdp[j + k] = true;
                    }
                }
            }
            dp = move(newdp);
        }
        int i;
        for (i = 0; target + i <= MAXNUMBER * m || target - i >= 0; ++i) {
            if ((target + i <= MAXNUMBER * m && dp[target + i]) || (target - i >= 0 && target - i <= MAXNUMBER * m && dp[target - i])) return i;
        }
        return i;
    }
};
```

Time complexity: O(m * max(mat[i][j]) * m * n) = 24,010,000,
space complexity: O(max(mat[i][j]) * m).

One WA: because each row must select one number and cannot select nothing. Therefore `dp` needs a new array and cannot reuse the original one.

One Runtime Error: `target` may be greater than `MAXNUMBER * m`, so an additional check is necessary.

## 1982. Find Array Given Subset Sums

Only 50 people in the world solved it. I thought about it for half an hour and still had no clue. I decisively gave up and helped my girlfriend make Coca-Cola chicken wings. Although I did not AK, I did get to eat delicious chicken.

After the contest, I naturally referred to [LingShen's solution](https://leetcode-cn.com/problems/find-array-given-subset-sums/solution/cong-zi-ji-de-he-huan-yuan-shu-zu-by-lee-aj8o/). YYDS.
