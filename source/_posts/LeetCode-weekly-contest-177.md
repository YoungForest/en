---
title: LeetCode Weekly Contest 177
date: 2020-02-23 12:12:46
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2020/02/23/LeetCode-weekly-contest-177/
  en: https://youngforest.github.io/en/2020/02/23/LeetCode-weekly-contest-177/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 333 / 6106 |	YoungForest | 	20 | 1:04:22 | 0:25:00 | 0:33:40 | 0:43:21 | 0:59:22  1 |

A contest about speed and being bug-free.

## 1360. Number of Days Between Two Dates

Calculate the difference between two dates. I originally wanted to compute it manually, but it was too complicated to write. Later I decisively gave up and opportunistically used Python's date handling library functions.

Time complexity: O(1),
space complexity: O(1).

```python
from datetime import date

class Solution:
    def daysBetweenDates(self, date1: str, date2: str) -> int:
        delta = date.fromisoformat(date1) - date.fromisoformat(date2)
        return abs(delta.days)
```

## 1361. Validate Binary Tree Nodes

Observation: a valid binary tree must satisfy:
1. exactly one node has no parent
2. every remaining node has exactly one parent
3. there are no orphans, meaning nodes with neither a parent nor a child, when the number of nodes is greater than 1

The third requirement is relatively hard to think of; even the OJ was initially wrong. I just went along with the wrong behavior and passed. Thanks to [votrubac](<https://leetcode.com/problems/validate-binary-tree-nodes/discuss/517596/Count-Parents-and-Orphans-O(n)>) for the hint.

Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    bool validateBinaryTreeNodes(int n, vector<int>& leftChild, vector<int>& rightChild) {
        if (n < 2)
            return true;
        vector<int> parents(n, -1);
        vector<bool> hasChild(n, false);
        auto fillParents = [&](int index, int parent) -> bool {
            if (parents[index] != -1)
                return false;
            parents[index] = parent;
            return true;
        };
        for (int i = 0; i < n; ++i) {
            for (int child : {leftChild[i], rightChild[i]}) {
                if (child != -1) {
                    if(fillParents(child, i) == false)
                        return false;
                }
            }
            hasChild[i] = leftChild[i] != -1 || rightChild[i] != -1;
        }
        int count = 0;
        for (int i : parents) {
            if (i == -1) {
                if (hasChild[i] == false)
                    return false;
                ++count;
            }
        }
        return count == 1;
    }
};
```

## 1362. Closest Divisors

Use a greedy strategy. Start enumerating from `sqrt(n)`; this gives the minimum difference.

Time complexity: O(sqrt(n)),
space complexity: O(1).

```cpp
class Solution {
    pair<int, int> current(int n) {
        for (int i = floor(sqrt(n)); i >= 1; --i) {
            if (n % i == 0) {
                return {i, n / i};
            }
        }
        return {0, 0};
    }
public:
    vector<int> closestDivisors(int num) {
        auto p1 = current(num + 1);
        auto p2 = current(num + 2);
        if (abs(p1.first - p1.second) < abs(p2.second - p2.first)) {
            return {p1.first, p1.second};
        } else {
            return {p2.first, p2.second};
        }
    }
};
```

## 1363. Largest Multiple of Three

Overall this is a greedy idea.
First consider a simpler problem:
How to form the largest array -> put the largest values first.
After adding the constraint of divisibility by 3,
we observe that keeping more remaining digits is better, and secondarily, removing smaller digits is better.
This gives the following algorithm.

Because I mistakenly used `multiset` as `set`, I got one Wrong Answer. This is not the first time I have made a similar mistake. In the future, when considering `set`, I need to think in advance about whether duplicates are allowed.

Time complexity: O(n log n),
space complexity: O(n).

```cpp
class Solution {
    // n log n
public:
    string largestMultipleOfThree(vector<int>& digits) {
        vector<multiset<int>> remind(3);
        int all = 0;
        for (int i : digits) {
            remind[i % 3].insert(i);
            all += i;
        }
        auto remove = [&](int one, int two) -> bool {
            if (remind[one].empty()) {
                if (remind[two].size() >= 2) {
                    remind[two].erase(remind[two].begin());
                    remind[two].erase(remind[two].begin());
                } else {
                    return false;
                }
            } else {
                remind[one].erase(remind[one].begin());
            }
            return true;
        };
        if (all % 3 == 0) {
            
        } else if (all % 3 == 1) {
            if (remove(1, 2) == false)
                return "";
        } else {
            if (remove(2, 1) == false)
                return "";
        }
        vector<int> v;
        for (const auto& s : remind) {
            for (int i : s) {
                v.push_back(i);
            }
        }
        sort(v.begin(), v.end(), greater<int>());
        string ans;
        for (int i : v) {
            ans += to_string(i);
        }
        if (ans[0] == '0')
            return "0";
        else
            return ans;
    }
};
```
