---
title: LeetCode Weekly Contest 245
date: 2021-06-13 17:28:42
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/06/13/LeetCode-weekly-contest-245/
  en: https://youngforest.github.io/en/2021/06/13/LeetCode-weekly-contest-245/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 1904 / 12724 | YoungForest | 12 | 1:39:20 | 0:02:52 | 1:24:20 🐞 3 | 0:21:30 | null |

LingShen's big data:

1897,Redistribute Characters to Make All Strings Equal,redistribute-characters-to-make-all-strings-equal,1309.1422268153
1898,Maximum Number of Removable Characters,maximum-number-of-removable-characters,1912.8440554296
1899,Merge Triplets to Form Target Triplet,merge-triplets-to-form-target-triplet,1635.6879273926
1900,The Earliest and Latest Rounds Where Players Compete,the-earliest-and-latest-rounds-where-players-compete,2454.7653333657

Today's weekly contest went badly. For the second problem, I initially miscalculated the time complexity and kept trying to find a better algorithm. Later, after seeing that 80 people had submitted, I re-examined the time complexity of binary-search brute force and found that it was actually fine. During implementation I hit one WA (when checking subsequence, I forgot to update the index of `s` for equal characters), and two TLEs (the removed indices cannot be marked with `unordered_set`, but should use `vector`. This counts as being killed by constant factors). This week I have to do Cruel Coding check-in again. Fortunately, because my results in the previous two weeks were good, after this week's Cruel Coding board update my rank did not drop but instead rose.

## 1897. Redistribute Characters to Make All Strings Equal

A warm-up problem. In essence, determine whether all characters can be evenly distributed among `n` words.

```cpp
class Solution {
public:
    bool makeEqual(vector<string>& words) {
        const int n = words.size();
        vector<int> cnt(26, 0);
        for (const auto& word : words) {
            for (char c : word) {
                ++cnt[c - 'a'];
            }
        }
        for (int i : cnt) {
            if (i % n != 0) return false;
        }
        return true;
    }
};
```

Time complexity: O(sum(words[i].length)),
space complexity: O(1).

## 1898. Maximum Number of Removable Characters

Turn an optimization problem into a decision problem (two pointers to check whether it is a subsequence), then use binary search.

```cpp
class Solution {
    using ll = int;
    vector<bool> mark = vector<bool>(1e5);
public:
    int maximumRemovals(string s, string p, vector<int>& removable) {
        const int n = removable.size();
        auto binary = [&](ll lo, ll hi, function<bool(const ll)> predicate) -> int {
            while (lo < hi) {
                ll mid = lo + (hi - lo) / 2;
                if (!predicate(mid)) {
                    hi = mid;
                } else {
                    lo = mid + 1;
                }
            }
            return lo;
        };
        // cout << s.size() << endl;
        // cout << n << endl;
        return binary(0, n + 1, [&](const ll x) -> bool {
            for (int i = 0; i < s.size(); ++i) {
                mark[i] = false;
            }
            for (int i = 0; i < x; ++i) {
                mark[removable[i]] = true;
            }
            int pi = 0, si = 0;
            while (pi < p.size()) {
                while (si < s.size() && (mark[si] || s[si] != p[pi])) ++si;
                if (si == s.size()) 
                {
                    // cout << x << ":false"  << endl;
                    return false;
                }
                // if (s[si] != p[pi]) cout << "impossible" << endl;
                ++si;
                ++pi;
            }
            // cout << x << ":true "<< si   << endl;
            return true;
        }) - 1;
    }
};
```

Time complexity: O(log removable.length * (s.length + p.length)),
space complexity: O(s.length).

The pitfall in this problem is that when marking which positions in `s` have been removed, you cannot use `unordered_set` (even with `reserve`), or it will time out. You can use `vector<bool>`. This counts as being killed by constant factors.

## 1899. Merge Triplets to Form Target Triplet

Because each merge operation takes the maximum value, any `triplet` with a value greater than `target` cannot be used.

So merge all the remaining usable triplets and see whether they can reach `target`.

```cpp
class Solution {
public:
    bool mergeTriplets(vector<vector<int>>& triplets, vector<int>& target) {
        vector<int> cnt(3, 0);
        for (int i = 0; i < 3; ++i) {
            for (const auto& v : triplets) {
                if (v[i] == target[i] && v[(i + 1) % 3] <= target[(i + 1) % 3] && v[(i + 2) % 3] <= target[(i + 2) % 3]) {
                    ++cnt[i];
                }
            }
        }
        for (int i = 0; i < 3; ++i) {
            if (cnt[i] == 0) return false;
        }
        return true;
    }
};
```

Time complexity: O(triplets.length),
space complexity: O(1).

## 1900. The Earliest and Latest Rounds Where Players Compete

TNL~
