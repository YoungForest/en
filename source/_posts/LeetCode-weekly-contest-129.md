---
title: LeetCode weekly contest 129
date: 2019-03-31 10:24:51
tags:
- Competitive Programming
categories:
- LeetCode
---

Last weekend, because I was preparing for Google's Kick Start Round A, I skipped one LeetCode weekly contest. But I still made up the LeetCode problems that evening. The four problems were not easy, but after thinking through them I solved them independently. It was a small comfort after Kick Start had beaten me into silence.

## 1020. Partition Array Into Three Parts With Equal Sum

Intuition:
One pass. Find the pivots at 1/3 and 2/3 of the total sum.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
public:
    bool canThreePartsEqualSum(vector<int>& A) {
        int sum_all = accumulate(A.begin(), A.end(), 0);
        if (sum_all %3 != 0) return false;
        int sum_3 = sum_all / 3;
        int sum_prefix = 0;
        for (int i = 0; i < A.size(); i++) {
            if (sum_prefix == sum_3 && sum_3 != sum_all)
                sum_3 += sum_all / 3;
            sum_prefix += A[i];
        }
        return sum_3 == sum_all;
    }
};
```

## 1022. Smallest Integer Divisible by K

Intuition:
Simulate the long-division process.
If the last digit is not 1, 3, 7, or 9, directly return -1. That means no valid `N` can be found.
Then use long division: keep making the last digit equal to 1 until all preceding digits are also 1.
Since a solution must exist in that case, the loop will eventually terminate.

Time complexity: O(result.length),
space complexity: O(1).

```cpp
class Solution {
    bool all11(int i) {
        while (i > 0) {
            if (i % 10 != 1) return false;
            i /= 10;
        }
        return true;
    }
public:
    int smallestRepunitDivByK(int K) {
        if (K % 10 != 1 && K % 10 != 3 && K % 10 != 7 && K % 10 != 9) return -1;
        vector<int> dictionary(10);
        vector<int> gewei(10);
        for (int i = 0; i < dictionary.size(); i++) {
            dictionary[i] = K * i;
            gewei[K * i % 10] = i;
        }
        int ret = 0;
        int remain = K;
        while (!all11(remain)) {
            int last = remain % 10;
            int new_value = 0;
            new_value = gewei[(11 - last) % 10] * K;
            ret++;
            remain = (new_value + remain) / 10;
        }
        while (remain > 0) {
            if (remain % 10 != 1) return -1;
            remain /= 10;
            ret++;
        }
        
        return ret;
    }
};
```

## 1021. Best Sightseeing Pair

Intuition:
Dynamic programming. The optimal substructure is:
the maximum score of a pair ending at the `i`-th element equals `max(pair formed by i and i-1, pair formed by i and the pair ending at i-1)`.

Time complexity: O(N), one pass.
Space complexity: O(1). Although the code below is implemented as O(N), the DP process only uses `dp[i-1]`, so it can be further optimized to O(1).

```cpp
class Solution {
public:
    int maxScoreSightseeingPair(vector<int>& A) {
        int n = A.size();
        vector<int> dp(n, 0);
        int ret = 0;
        for (int i = 1; i < n; i++) {
            dp[i] = max(A[i] + A[i - 1] - 1, dp[i - 1] - A[i - 1] + A[i] - 1);
            ret = max(ret, dp[i]);
        }
        
        return ret;
    }
};
```

## 1023. Binary String With Substrings Representing 1 To N

Intuition:
Build a table first, then query the table.

Time complexity: O(max(S.length, N)),
space complexity: O(N).

```cpp
class Solution {
public:
    bool queryString(string S, int N) {
        vector<bool> contain(N + 1);
        for (size_t i = 0; i < S.size(); i++) {
            int value = S[i] - '0';
            contain[value] = true;
            for (size_t j = 1; j < 31 && i + j < S.size(); j++) {
                value = (value << 1) + S[i + j] - '0';
                if (value <= N) contain[value] = true;
            }
        }
        
        for (int i = 1; i <= N; i++) {
            if (!contain[i]) return false;
        }
        return true;
    }
};
```

## Postscript

I still have not organized my notes for Google Kick Start Round A. The three-hour contest was, in practice, only two hours for me. I solved the warm-up problem and the small test set of the second problem. In the last hour I had no clue at all, and I knew that even with more time I would not make a breakthrough, so I gave up. My rank was 600. It seems there was a bug in the contest system, and submissions were unavailable for the last 25 minutes; otherwise my rank might have dropped further, because many people submit another wave of code near the end.

Kick Start happens once a month. I want to continue participating in Round B next time and keep taking the beating. According to Teacher Tang, for Google campus recruiting in China, only Rounds D and E matter. I am still far away! I still need to keep grinding problems and also fill in my knowledge of competitive programming and CS. Even if I do not get into Google in the end, the job-search result should not be bad.
Keep going, Forest!
