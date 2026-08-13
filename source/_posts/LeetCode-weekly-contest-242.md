---
title: LeetCode Weekly Contest 242
date: 2021-05-23 10:30:24
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/05/23/LeetCode-weekly-contest-242/
  en: https://youngforest.github.io/en/2021/05/23/LeetCode-weekly-contest-242/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 165 / 12400 | YoungForest | 18 | 	1:07:14 | 0:03:22 🐞1 | 0:16:01 🐞1 | 0:27:41  🐞1 | 0:52:14 |

AK 40 minutes early. Although because of carelessness I had one WA on each of the first three problems, causing 15 minutes of penalty time, the good news is that I probably will not need to do daily Cruel Coding check-ins this week. Perfect timing to go all in on preparing for Wednesday's master's thesis defense.

My three years of master's study all depend on Wednesday. Finish it in one battle. Keep going, Forest!

## 1869. Longer Contiguous Segments of Ones than Zeros

A warm-up problem. Count the length of each contiguous substring and update the longest length.

Because I put the code for updating the longest length in the wrong place, inside the `if` branch, I got one WA. It should be updated no matter what, so it needs to be outside.

```cpp
class Solution {
public:
    bool checkZeroOnes(string s) {
        vector<int> longest(2, 0);
        int length = 0;
        char last = '2';
        for (char c : s) {
            if (c != last) {
                last = c;
                length = 1;
            } else {
                ++length;
            }
            longest[c - '0'] = max(longest[c - '0'], length);
        }
        return longest[1] > longest[0];
    }
};
```

Time complexity: O(N),
space complexity: O(1).

## 1870. Minimum Speed to Arrive on Time

Directly brute-force with binary search. The binary-search template I summarized earlier is very useful. Basically, only a few lines need to be changed.

I got one WA because of floating-point precision. `10^-5` is not small enough; I suggest using `10^-9` in the future.

```cpp
class Solution {
    using ll = int;
public:
    int minSpeedOnTime(vector<int>& dist, double hour) {
        int lo = 1;
        int hi = 1e7 + 1;
        // f f f t t t
        auto binary = [&](ll lo, ll hi, function<bool(const ll)> predicate) -> int {
            // return first true
            while (lo < hi) {
                ll mid = lo + (hi - lo) / 2;
                if (predicate(mid)) {
                    hi = mid;
                } else {
                    lo = mid + 1;
                }
            }
            return lo;
        };
        auto pred = [&](const ll x) -> bool {
            double ans = 0;
            int i;
            for (i = 0; i + 1 < dist.size(); ++i) {
                ans += (dist[i] + x - 1) / x;
            }
            ans += dist[i] / static_cast<double>(x);
            // cout << x << " " << ans << endl;
            return ans <= hour + 1e-9;
        };
        ll ans = binary(lo, hi, pred);
        if (ans >= hi) return -1;
        else return ans;
    }
};
```

Time complexity: O(log dist[i] * dist.length) = log 10^5 * 10^5,
space complexity: O(1).

## 1871. Jump Game VII

It is easy to think of an O(N^2) brute-force solution.

Traverse from the beginning. If the current character is 0, update all reachable positions afterward.

The time complexity is: s.length * (maxJump - minJump) = 10% * 10^5.

The optimization direction is "update all reachable positions afterward". Obviously, this update has many repeated updates and therefore costs a lot. But if we think about it, each time we are actually updating an interval, the interval sizes are equal, and the intervals always move to the right. Therefore, we find that updating an interval does not require traversing `minJump...maxJump`; we only need to traverse backward from `maxJump`, and if it overlaps with an already-updated interval, directly break and end the update. Since each position is updated at most once, the time complexity is s.length.

```cpp
class Solution {
public:
    bool canReach(string s, int minJump, int maxJump) {
        // brute-force: s.length * (maxJump - minJump) = 10^5 * 10^5
        // better: s.length * 1
        const int n = s.size();
        
        vector<bool> reachable(n, false);
        reachable[0] = true;
        // <= reach
        for (int i = 0; i < n; ++i) {
            if (s[i] == '0') {
                if (reachable[i]) {
                    for (int j = min(maxJump, n - 1 - i); j >= minJump; --j) {
                        if (reachable[i + j]) break;
                        reachable[i + j] = true;
                    }
                }
            }
        }
        return s[n-1] == '0' && reachable[n - 1];
    }
};
```

Time complexity: O(s.length),
space complexity: O(s.length).

## 1872. Stone Game VIII

Similar to previous Stone Game problems, most optimal-play problems like this belong to Dynamic Programming.

That is, search through all possible choices to find the optimal result. There are many repeated subproblems in the process, so DP is needed.

The optimization target is `(Alice's score - Bob's score)`. Alice wants to maximize it, and Bob wants to minimize it. In fact, both are maximizing `(my score - the opponent's score)`.

Also, observe that the result of the operation is actually the sum of stones, so the prefix-sum array needs to be computed in advance.

The problem transforms into: pick a number from `prefix sum[i:]` to maximize the score difference; afterward, the next player can only pick from after the chosen position.

Define:

`dp(i)` as the maximum score difference when picking a number from `prefix sum[i:]`.

Then the state transition is:

`dp(i) = max(presum[j] + dp(j + 1) for j in range(i,))`.

The time complexity is O(N^2), which obviously times out.

However, in the state transition there are actually overlapping subproblems as well. The for-loop max only needs to be computed once, so it can be further optimized to O(N).

```cpp
class Solution {
public:
    int stoneGameVIII(vector<int>& stones) {
        // presum
        // i = 0
        // pick i, ..., n - 1
        // time: n ^ 2
        const int n = stones.size();
        vector<int> presum(n + 1);
        presum[0] = 0;
        for (int i = 0; i < n; ++i) {
            presum[i+1] = presum[i] + stones[i];
        }
        int maxDp = numeric_limits<int>::min();
        vector<int> dp(n + 2, 0);
        dp[n+1] = 0;
        for (int i = n; i >= 2; --i) { // 这里需要注意结束条件，因为第一次挑选不能不合并或只合并第一个，因此presum[0],presum[1]是没用的。
            maxDp = max(maxDp, presum[i] - dp[i + 1]);
            dp[i] = maxDp;
        }
        return maxDp;
    }
};
```

Time complexity: O(N),
space complexity: O(N).
