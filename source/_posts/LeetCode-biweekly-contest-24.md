---
title: LeetCode Biweekly Contest 24
date: 2020-04-19 05:37:57
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 700 / 7729 |	YoungForest | 18 | 	1:17:49 | 0:04:50 |  0:10:38 | 0:17:45 |   1:02:49 3 |

Recently I participated in two Codeforces contests, and my CF rating is hovering around 1400+. Codeforces problems are much harder than LeetCode's. I did not manage to keep going. This is also related to CF not having a very good discuss section; after each contest ends, I can only read the official editorial.

## 1413. Minimum Value to Get Positive Step by Step Sum

Track the most negative prefix sum. One thing to note is that `startValue` must be positive.

Time complexity: O(N),
space complexity: O(N).

```python
class Solution:
    def minStartValue(self, nums: List[int]) -> int:
        min_sum = 0
        current_sum = 0
        for i in nums:
            current_sum += i
            min_sum = min(current_sum, min_sum)
        return 1 - min_sum if min_sum <= 0 else 1
```

## 1414. Find the Minimum Number of Fibonacci Numbers Whose Sum Is K

Greedy. Always choose the largest Fibonacci number that is less than or equal to `i`, then recursively solve the remaining value.

Time complexity: O((log k)^2),
space complexity: O(log k).


```python
class Solution:
    def findMinFibonacciNumbers(self, k: int) -> int:
        def findLargestFibonacciLessEqualThan(i):
            a = 1
            b = 1
            while a <= i:
                a, b = a + b, a
            return b
        
        def dp(i):
            x = findLargestFibonacciLessEqualThan(i)
            if x == i:
                return 1
            else:
                return 1 + dp(i - x)
        return dp(k)
```

## 1415. The k-th Lexicographical String of All Happy Strings of Length n

Use backtracking to enumerate all Happy strings in lexicographical order until the `k`-th one is found.

Time complexity: O(min(3 ^ n, k)),
space complexity: O(n).

```cpp
class Solution {
public:
    string getHappyString(int n, int k) {
        int rank = 0;
        string ans;
        function<void(string&)> backtracking = [&](string& current) -> void {
            if (current.size() == n) {
                ++rank;
                if (rank == k) {
                    ans = current;
                }
            } else {
                char last = current.empty() ? '0' : current.back();
                for (char c : {'a', 'b', 'c'}) {
                    if (c == last) continue;
                    current.push_back(c);
                    backtracking(current);
                    if (rank >= k) return;
                    current.pop_back();
                }
            }
        };
        string current;
        backtracking(current);
        end:;
        return ans;
    }
};
```

## 1416. Restore The Array

DP. `dp[i]`: the number of ways to split the array ending at the `i`-th element.
`dp[i] = sum(dp[j] for j + 1, ..., i can form a valid number)`

Time complexity: O(N * log K),
space complexity: O(N).

```python
class Solution:
    def numberOfArrays(self, s: str, k: int) -> int:
        dp = [0] * (10**5 + 5)
        dp[0] = 1
        for index in range(1, len(s)):
            i = index
            ans = 0
            current = int(s[i])
            base = 1
            while current <= k and 10**(index - i) <= k and i - 1 >= 0:
                ans += dp[i-1] if s[i] != '0' else 0
                i -= 1
                base *= 10
                current = current + int(s[i]) * base
            if current <= k and 10**(index - i) <= k:
                ans += 1
            dp[index] = ans
            
        return dp[len(s) - 1] % int(10**9 + 7)
```
