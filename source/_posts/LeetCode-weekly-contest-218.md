---
title: LeetCode Weekly Contest 218
date: 2020-12-06 16:45:12
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 71 / 9827 | YoungForest | 18 | 	0:48:21 | 0:03:32	 | 0:05:23 |  0:12:14 | 0:43:21  1 |


## 1678. Goal Parser Interpretation

Warm-up problem. String interpretation. A general approach is to first do lexical analysis to get tokens, then translate them in sequence. Since this problem has few tokens and simple settings, it can be handled in one pass by looking ahead to determine the token.

```cpp
class Solution {
public:
    string interpret(string command) {
        string ans;
        for (int i = 0; i < command.size(); ++i) {
            if (command[i] == 'G') {
                ans.push_back('G');
            } else {
                if (command[i + 1] == ')') {
                    ans.push_back('o');
                    ++i;
                } else {
                    ans.push_back('a');
                    ans.push_back('l');
                    i += 3;
                }
            }
        }
        return ans;
    }
};
```

Time complexity: O(N),
space complexity: O(N).

It is said that Python contestants solved it in one line.

## 1679. Max Number of K-Sum Pairs

Greedy. For each number, find its corresponding number and remove it. Use a hash table to store previously seen numbers, so the lookup cost is O(1).

```cpp
class Solution {
public:
    int maxOperations(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());
        int ans = 0;
        unordered_map<int, int> count;
        for (int i : nums) {
            if (count[k - i] > 0) {
                ++ans;
                --count[k - i];
            } else {
                ++count[i];
            }
        }
        return ans;
    }
};
```

Time complexity: O(N). The sorting in the code is actually unnecessary; during the contest I did not notice and sorted first.
Space complexity: O(N).

## 1680. Concatenation of Consecutive Binary Numbers

Straightforward. Use multiplication and addition to simulate concatenation.

One thing to note is whether the left shift operation can directly take modulo. The answer is yes: addition, subtraction, and multiplication can all take modulo directly, but division cannot.
Left shift is equivalent to multiplication.

```cpp
class Solution {
    using ll = long long;
    const ll MOD = 1e9 + 7;
    ll bits(ll x) {
        ll ans = 0;
        while (x > 0) {
            x = x >> 1;
            ++ans;
        }
        return ans;
    }
public:
    int concatenatedBinary(int n) {
        ll ans = 0;
        for (ll i = 1; i <= n; ++i) {
            ans = ((ans << bits(i)) + i) % MOD;
        }
        return ans;
    }
};
```

Time complexity: O(N * log N),
space complexity: O(1).

## 1681. Minimum Incompatibility

This problem is labeled medium, but it is worth 6 points, so it is indeed not easy.
The correct approach is state-compression DP; see the discussion section for details. But during the contest, many people ACed it, including me, using a brute-force backtracking solution. Because the time complexity of backtracking + pruning is often hard to analyze, even after it passes, I still do not feel confident.
Especially since LeetCode Weekly Contest recently added a rejudge mechanism, passing during the contest does not necessarily mean success, which makes me even more uneasy.

I first used a greedy idea, always trying to add the smallest number to a set, to obtain a possible solution.
This solution is not necessarily the minimum solution, but it can be used for later pruning.
Then backtracking searches the whole solution space, constructs solution sets, and obtains the minimum solution.

```cpp
class Solution {
public:
    int minimumIncompatibility(vector<int>& nums, int k) {
        const int n = nums.size();
        const int s = n / k;
        
        unordered_map<int, int> count;
        for (int i : nums) {
            ++count[i];
            if (count[i] > k) return -1;
        }
        auto greedy = [&]() -> int {
            int ans = 0;
            multiset<int> s(nums.begin(), nums.end());
            const int n = nums.size();
            for (int setIdx = 0; setIdx < k; ++setIdx) {
                int minValue = *s.begin();
                int maxValue = minValue;
                int current = minValue;
                s.erase(s.begin());
                int i = 1;
                while (i < n / k) {
                    auto it = s.upper_bound(current);
                    if (it == s.end()) return numeric_limits<int>::max();
                    current = *it;
                    s.erase(it);
                    maxValue = current;
                    ++i;
                }
                ans += maxValue - minValue;
            }
            return ans;
        };
        int ans = greedy();
        // if (ans != -1) return ans;
        vector<set<int>> results(k);
        vector<int> uncomp(k, 0);
        function<void(const int, const int)> backtracking = [&](const int i, const int condidate) -> void {
            if (condidate > ans) return;
            if (i == nums.size()) {
                ans = min(ans, condidate);
            } else {
                const int x = nums[i];
                for (int j = 0; j < k; ++j) {
                    if (results[j].size() < s) {
                        if (j - 1 >= 0 && results[j - 1].size() == 0) break;
                        if (results[j].find(x) == results[j].end()) {
                            const int tmpuncomp = uncomp[j];
                            results[j].insert(x);
                            uncomp[j] = *results[j].rbegin() - *results[j].begin();
                            backtracking(i + 1, condidate + uncomp[j] - tmpuncomp);
                            results[j].erase(x);
                            uncomp[j] = tmpuncomp;
                        }
                    }
                }
            }
        };
        backtracking(0, 0);
        if (ans == numeric_limits<int>::max()) return -1;
        else return ans;
    }
};
```

Time complexity: O((k * log (n / k)) ^ N). Because pruning exists, it runs faster in practice, but the real time complexity is hard to analyze.
Space complexity: O(N).
