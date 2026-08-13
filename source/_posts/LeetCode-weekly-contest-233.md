---
title: LeetCode weekly contest 233
date: 2021-03-25 09:43:36
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/03/25/LeetCode-weekly-contest-233/
  en: https://youngforest.github.io/en/2021/03/25/LeetCode-weekly-contest-233/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (7)|
|--|--|--|--|--|--|--|--|
| 923 / 12037 | YoungForest | 13 | 1:13:09 | 0:29:59 | 0:50:24 | 1:13:09 | null |

Over the weekend, I completely wasted three contests.

I did not participate in the biweekly contest, was half an hour late to the weekly contest, and then immediately participated in Kick Start. I was already done for.

In the future, before contests, I still need to conserve energy and compete properly.

For the fourth problem, I actually had an idea at the end, but unfortunately there was not enough time. I had previously done similar problems using Trie to handle XOR, so the impression was still quite deep.

<!-- more -->

## 1800. Maximum Ascending Subarray Sum

A warm-up problem. Use two variables to record the current accumulated sum satisfying the increasing condition and the previous element, then update the maximum accumulated sum.

```cpp
class Solution {
public:
    int maxAscendingSum(vector<int>& nums) {
        int ans = 1;
        int last = 0;
        int current = 0;
        for (int i : nums) {
            if (i > last) {
                current += i;
            } else {
                current = i;
            }
            ans = max(ans, current);
            last = i;
        }
        return ans;
    }
};
```

Time complexity: O(N),
Space complexity: O(1).

## 1801. Number of Orders in the Backlog

Use `TreeMap` to maintain price and quantity as key-value pairs. Update according to the problem statement.

```cpp
template <typename A, typename B>
ostream& operator <<(ostream& out, const pair<A, B>& a) {
  out << "(" << a.first << "," << a.second << ")";
  return out;
}
template <typename U, typename T, class Cmp>
ostream& operator <<(ostream& out, const map<U, T, Cmp>& a) {
  out << "{"; bool first = true;
  for (auto& p : a) { out << (first ? "" : ", "); out << p.first << ":" << p.second; first = 0;} out << "}";
  return out;
}
class Solution {
    using ll = long long;
    const int MOD = 1e9 + 7;
public:
    int getNumberOfBacklogOrders(vector<vector<int>>& orders) {
        // 0 buy 1 sell
        map<int, ll> buy, sell;
        for (const auto & v : orders) {
            int price = v[0], amount = v[1];
            if (v[2] == 0) {
                // buy
                for (auto& p : sell) {
                    if (amount <= 0 || p.first > price) break;
                    if (p.second >= amount) {
                        p.second -= amount;
                        amount = 0;
                    } else {
                        amount -= p.second;
                        p.second = 0;
                    }
                }
                while (!sell.empty() && sell.begin()->second == 0) {
                    sell.erase(sell.begin());
                }
                if (amount > 0) {
                    buy[price] += amount;
                }
            } else {
                // sell
                for (auto it = buy.rbegin(); it != buy.rend(); ++it) {
                    auto& p = *it;
                    if (amount <= 0 || p.first < price) break;
                    if (p.second >= amount) {
                        p.second -= amount;
                        amount = 0;
                    } else {
                        amount -= p.second;
                        p.second = 0;
                    }
                }
                while (!buy.empty() && buy.rbegin()->second == 0) {
                    buy.erase(prev(buy.end()));
                }
                if (amount > 0) {
                    sell[price] += amount;
                }
            }
        }
        ll ans = 0;
        // cout << buy << endl;
        // cout << sell << endl;
        for (const auto& p : buy) {
            ans = (ans + p.second) % MOD;
        }
        for (const auto& p : sell) {
            ans = (ans + p.second) % MOD;
        }
        return ans;
    }
};
```

Time complexity: O(orders.length * log orders.length),
Space complexity: O(orders.length).

## 1802. Maximum Value at a Given Index in a Bounded Array

Binary search. Transform the optimization problem into a decision problem.

After limiting the maximum value, use the arithmetic progression sum formula to calculate the total sum.

```cpp
class Solution {
    using ll = long long;
public:
    int maxValue(int n, int index, int maxSum) {
        // peek, peek - 1, ..., 1
        // Sn=n*a1+n(n-1)d/2
        // Sn=n(a1+an)/2
        auto oneSide = [&](const ll peek, const ll index) -> ll {
            if (index + 1 >= peek) {
                return ((peek) * (peek + 1) / 2) + index + 1 - peek;
            } else {
                return (index + 1) * peek + (index + 1) * (index) * (-1) / 2;
            }
        };
        // 0 1 2 3 4 5
        // n = 6
        // index = 2
        auto sumOf = [&](const ll peek) -> ll {
            // cout << "oneSide " << oneSide(peek, index) << " " << oneSide(peek, n - index - 1) << endl;
            return oneSide(peek, index) + oneSide(peek, n - index - 1) - peek;
        };
        // first true
        auto binary = [&](ll lo, ll hi, function<bool(const ll)> predicate) -> ll {
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
        // f f f t t t
        // cout << sumOf(3) << " "<< sumOf(2) << " " << sumOf(1) << endl;
        return binary(1, 1e9 + 7, [&](const ll x) -> bool {
            return sumOf(x) > maxSum;
        }) - 1;
    }
};
```

Time complexity: O(log maxSum),
Space complexity: O(1).

## 1803. Count Pairs With XOR in a Range

Similar problems: LC 421 and 1707.

Use Trie to solve XOR problems. The time complexity is generally O(31 * N).

```cpp
class Solution {
    struct Trie {
        array<Trie*, 2> children;
        int count = 0;
    };
    const int MAX_BIT = 16;
public:
    int countPairs(vector<int>& nums, int low, int high) {
        const int n = nums.size();
        Trie *root = new Trie();
        for (int i : nums) {
            auto current = root;
            for (int k = MAX_BIT; k >= 0; --k) {
                if (current->children[(i >> k) & 1] == nullptr) {
                    current->children[(i >> k) & 1] = new Trie();
                }
                current = current->children[(i >> k) & 1];
                current->count += 1;
            }
        }
        auto countLessThan = [&](const int target) -> int {
            int ans = 0;
            for (int i : nums) {
                auto current = root;
                for (int k = MAX_BIT; k >= 0 && current; --k) {
                    const int x = (i >> k) & 1;
                    const int y = (target >> k) & 1;
                    if (y == 1) {
                        if (current->children[x]) {
                            ans += current->children[x]->count;     // take all xor = zero
                        }
                        current = current->children[1 - x];
                    } else {
                        current = current->children[x];
                    }
                }
            }
            return ans;
        };
        return (countLessThan(high + 1) - countLessThan(low)) / 2;
        
    }
};
```

Time complexity: O(16 N),
Space complexity: O(16 N).
