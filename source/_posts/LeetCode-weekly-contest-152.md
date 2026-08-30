---
title: LeetCode weekly contest 152
date: 2019-09-01 19:52:40
description: Weekly notes on prime arrangements, diet windows, palindrome queries and seven-letter puzzles, plus two careless penalties.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/09/01/LeetCode-weekly-contest-152/
  en: https://youngforest.github.io/en/2019/09/01/LeetCode-weekly-contest-152/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-152/en-hero.webp" alt="Forest corrects two mis-set levers beside a prime bead sorter, diet window, parity lanterns and seven-petal puzzle wheel" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 111 / 5333 |	YoungForest | 	18	 | 	1:11:49 | 0:11:56  1 | 0:21:44  1 | 0:37:27 | 1:01:49 |

In this contest, because of carelessness, I forgot to consider the corner case in the first problem: the permutation count of 0 is 1. In the second problem, I simply reversed `upper` and `lower`. I got two penalties. Otherwise I probably could have entered the top 100. The problems were relatively simple, all standard problems, and previous original problems could be adapted with small changes.

## 1175. Prime Arrangements

Sieve for primes + permutations and combinations.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
    using ll = long long;
    const int modulo = 1e9 + 7;
    unordered_map<int, ll> memo;
    ll A(int x) {
        if (x == 0)
            return 1;
        if (x == 1)
            return 1;
        if (memo.find(x) == memo.end()) {
            memo[x] = (x * A(x - 1)) % modulo;
        }
        return memo[x];
    }
public:
    int numPrimeArrangements(int n) {
        vector<bool> a(n + 1, true);
        int count = 0;
        for (int i = 2; i < n + 1; ++i) {
            if (a[i]) {
                ++count;
                for (int j = 2; j * i < n + 1; ++j) {
                    a[j * i] = false;
                }
            }
        }
        return (A(count) * A(n - count)) % modulo;
    }
};
```

## 1176. Diet Plan Performance

A simple sliding window. I actually got it wrong once because of carelessness.

Time complexity: O(N),
space complexity: O(N).


```cpp
class Solution {
public:
    int dietPlanPerformance(vector<int>& calories, int k, int lower, int upper) {
        if (k > calories.size())
            return 0;
        int sum = accumulate(calories.begin(), calories.begin() + k, 0);
        int ans = 0;
        if (sum > upper)
            ++ans;
        else if (sum < lower)
            --ans;
        for (int i = k; i < calories.size(); ++i) {
            sum = sum + calories[i] - calories[i - k];
            if (sum > upper)
                ++ans;
            else if (sum < lower)
                --ans;
        }
        return ans;
    }
};
```

## 5175. Can Make Palindrome from Substring

Be sensitive to palindrome strings. Observation: a palindrome allows only one letter to have an odd count. Each replacement allows two additional odd counts.

Time complexity: O(max(s.size(), queries.size()).
Space complexity: O(max(s.size(), queries.size()).

```cpp
class Solution {
public:
    vector<bool> canMakePaliQueries(string s, vector<vector<int>>& queries) {
        vector<int> count(26, 0);
        vector<vector<int>> prefix(s.size() + 1);
        prefix[0] = count;
        for (int i = 1; i < s.size() + 1; ++i) {
            ++count[s[i - 1] - 'a'];
            prefix[i] = count;
        }
        vector<bool> ans(queries.size());
        for (int j = 0; j < queries.size(); ++j) {
            const auto& query = queries[j];
            const auto& l = prefix[query[0]];
            const auto& r = prefix[query[1] + 1];
            int odd = 0;
            for (int i = 0; i < 26; ++i) {
                // cout << r[i] << ", " << l[i] << endl;
                int sub = r[i] - l[i];
                if (sub % 2 == 1) {
                    ++odd;
                }
            }
            ans[j] = odd <= 1 + 2 * query[2];
        }
        return ans;
    }
};
```

## 1178. Number of Valid Words for Each Puzzle

A variant of Trie.

First, the brute-force solution at least comes to mind: match every `puzzle` with every `word`. The time complexity is O(words.length * puzzles.length), and it will definitely time out.
The optimization direction is to aggregate the information of `word`, and Trie is a very good data structure for that.

Time complexity: O(2^puzzles[i].length * puzzles.length, words.length * words[i].length).
Space complexity: O(puzzles.length + min(2 ^ 26, words.length)).

```cpp
class Solution {
    struct Trie {
        shared_ptr<Trie> zero, one;
        int value = 0;
    };
    shared_ptr<Trie> root;
    int dfs(const vector<bool>& appear, int begin, shared_ptr<Trie> current, int special) {
        if (current == nullptr)
            return 0;
        if (special == begin) {
            return dfs(appear, begin + 1, current->one, special) + current->value;
        }
        if (appear[begin]) {
            return dfs(appear, begin + 1, current->zero, special) + dfs(appear, begin + 1, current->one, special) + current->value;
        } else {
            return dfs(appear, begin + 1, current->zero, special) + current->value;
        }
    }
public:
    vector<int> findNumOfValidWords(vector<string>& words, vector<string>& puzzles) {
        root = make_shared<Trie>();
        for (const string& w : words) {
            vector<bool> appear(26, false);
            for (char c : w) {
                appear[c - 'a'] = true;
            }
            auto current = root;
            for (bool b : appear) {
                if (b) {
                    if (current->one == nullptr) {
                        current->one = make_shared<Trie>();
                    }
                    current = current->one;
                } else {
                    if (current->zero == nullptr) {
                        current->zero = make_shared<Trie>();
                    }
                    current = current->zero;
                }
            }
            ++current->value;
        }
        vector<int> ans(puzzles.size());
        for (int i = 0; i < puzzles.size(); ++i) {
            const auto& puzzle = puzzles[i];
            auto current = root;
            vector<bool> appear(26, false);
            for (char c : puzzle) {
                appear[c - 'a'] = true;
            }
            ans[i] = dfs(appear, 0, root, puzzle[0] - 'a');
        }
        return ans;
    }
};
```
