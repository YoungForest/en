---
title: LeetCode weekly contest 133
date: 2019-04-21 20:50:55
description: Late-start contest notes on distance-ordered cells, two-city scheduling, non-overlapping subarrays and reversing a stream-query tree.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/04/21/LeetCode-weekly-contest-133/
  en: https://youngforest.github.io/en/2019/04/21/LeetCode-weekly-contest-133/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-133/en-hero.webp" alt="Forest reverses a query tree beside distance rings, two-city assignments and two non-overlapping windows" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (5) |	Q4 (5)|
|--|--|--|--|--|--|--|--|
|864 / 4860	|	YoungForest |	14 | 	1:10:35 | 0:42:32 | 0:54:38 |	1:10:35 | null |

This week's problems improved quite a lot in quality compared with the previous few weeks. There were fewer trivial problems, and more algorithmic knowledge was tested. Even the first two Easy problems tested enough programming ability. In this contest, I lost half an hour at the beginning because of a stomachache, so I started late. The last problem was actually close to AC. The overall direction was right: using a `Trie`. But after submitting in the last 10 minutes, it TLE'd, and there was no time left to modify it. The construction direction of the Trie was reversed. For matching problems, we can go from front to back, or from back to front. For this problem, going from back to front is not only simpler to implement, but also better in time (even if the worst-case time complexity is the same).

## 1030. Matrix Cells in Distance Order
Intuition:
Sort all cells by distance.
Here I used a treemap, which keeps entries ordered after insertion. You can also construct a vector and then sort it.
Time complexity: O(R * C * log (R*C)).
Space complexity: O(R * C).

```cpp
class Solution {
public:
    vector<vector<int>> allCellsDistOrder(int R, int C, int r0, int c0) {
        multimap<int, pair<int, int>> distance;
        for (int i = 0; i < R; ++i) {
            for (int j = 0; j < C; ++j) {
                distance.insert({abs(r0 - i) + abs(c0 - j), {i, j}});
            }
        }
        vector<vector<int>> ret;
        for (const auto& item : distance) {
            ret.push_back(vector<int> { item.second.first, item.second.second});
        }

        return ret;
    }
};
```

At first, I tried to construct the result directly, but found it was not easy to write. I decisively gave up, though it still cost some time.
After reading discuss, I found that the mainstream solution still starts from construction. But using standard BFS makes it much easier to write.

## 1029. Two City Scheduling

Intuition:
Greedy.
Because each person must be assigned to one city, and the result asks for the minimum total cost,
sort by the cost difference between the two cities for each person. Send the first half to A and the second half to B.

Time complexity: O(N log N),
Space complexity: O(1).

```cpp
class Solution {
public:
    int twoCitySchedCost(vector<vector<int>>& costs) {
        sort(costs.begin(), costs.end(), [](const vector<int>& lhs, const vector<int>& rhs) -> bool {
            return lhs[1] - lhs[0] > rhs[1] - rhs[0];
        });
        int N = costs.size() / 2;
        
        int ret = 0;
        for (int i = 0; i < N; ++i) {
            ret += costs[i][0];
        }
        for (int i = N; i < 2*N; ++i) {
            ret += costs[i][1];
        }
        return ret;
    }
};
```

## 1031. Maximum Sum of Two Non-Overlapping Subarrays

Intuition: 
Somewhat brute-force: compute the sums of all subarrays of length L and M, then pick the maximum non-overlapping pair.

Time complexity: O(N ^ 2)
Space complexity: O(N)

```cpp
class Solution {
public:
    int maxSumTwoNoOverlap(vector<int>& A, int L, int M) {
        const int n = A.size();
        vector<int> sum_l(n, 0);
        vector<int> sum_m(n, 0);
        
        int current_sum = accumulate(A.begin(), A.begin() + L, 0);
        sum_l.at(0) = current_sum;
        for (int i = 1; i <= n - L; ++i) {
            current_sum += A[i + L - 1] - A[i - 1];
            sum_l.at(i) = current_sum;
        }
        
        current_sum = accumulate(A.begin(), A.begin() + M, 0);
        sum_m.at(0) = current_sum;
        for (int i = 1; i <= n - M; ++i) {
            current_sum += A[i + M - 1] - A[i - 1];
            sum_m.at(i) = current_sum;
        }
        
        int ret = 0;
        for (int i = 0; i <= n - L; ++i) {
            for (int j = i + L; j <= n - M; ++j) {
                ret = max(ret, sum_l.at(i) + sum_m.at(j));
            }
        }
        for (int i = 0; i <= n - M; ++i) {
            for (int j = i + M; j <= n - L; ++j) {
                ret = max(ret, sum_m.at(i) + sum_l.at(j));
            }
        }
        
        return ret;
    }
};
```

The one-pass solution uses DP.
Time complexity: O(N),
Space complexity: O(1).

```cpp
class Solution {
public:
    int maxSumTwoNoOverlap(vector<int>& A, int L, int M) {
        for (int i = 1; i < A.size(); ++i) {
            A[i] += A[i - 1];
        }
        // Lmax: 从头到当前位置再向前跳M个元素的子数组中，长度为L的子数组的最大和
        // Mmax: ... ... . .  .. . L ..   . . ..  . ..  .M.. ...
        int ret = A[L + M - 1], Lmax = A[L - 1], Mmax = A[M - 1];
        for (int i = L + M; i < A.size(); ++i) {
            Lmax = max(Lmax, A[i - M] - A[i - M - L]);
            Mmax = max(Mmax, A[i - L] - A[i - L - M]);
            ret = max({ret, A[i] - A[i - M] + Lmax, A[i] - A[i - L] + Mmax});
        }
        
        return ret;
    }
};
```

## 1032. Stream of Characters

Intuition:
A Trie can solve this kind of string matching query problem very well.
Matching from back to front is more intuitive, slightly better in time, and simpler to implement.

Time complexity: construction O(words.length * word.length), query O(word.length).
Space complexity: worst case O(words.length * word.length).

```cpp
class StreamChecker {
    // 单词树
    struct Trie {
        vector<shared_ptr<Trie>> data = vector<shared_ptr<Trie>>(26);
        bool value = false;
    };
    shared_ptr<Trie> root = make_shared<Trie>();
    vector<char> history;
public:
    StreamChecker(vector<string>& words) {
        for (const auto& word : words) {
            shared_ptr<Trie> t = root;
            for (int i = word.size() - 1; i >= 0; --i) {
                char c = word[i];
                if (t->data[c - 'a'] == nullptr)
                    t->data[c - 'a'] = make_shared<Trie>();
                t = t->data[c - 'a'];
            }
            t->value = true;
        }
    }
    
    bool query(char letter) {
        history.push_back(letter);
        shared_ptr<Trie> current = root;
        if (current->value)
            return true;
        for (int i = history.size() - 1; i >= 0; --i) {
            char c = history[i];
            if (current->data[c - 'a'] != nullptr) {
                current = current->data[c - 'a'];
            } else {
                return false;
            }
            if (current->value)
                return true;
        }
        return false;
    }
};

/**
 * Your StreamChecker object will be instantiated and called as such:
 * StreamChecker* obj = new StreamChecker(words);
 * bool param_1 = obj->query(letter);
 */
```

## Afterword

It has been two and a half months since a senior from Kuaishou asked me about new C++ features during Spring Festival. During this period, I read *C++ Primer*, and I am currently reading *Effective Modern C++* and *C++ Concurrency in Action*. I am confident that I understand the new features better now, and I am also trying to participate more in C++ projects and develop C++ as my main language. I hope this can become one of my core competitive advantages when I look for a job next year.
