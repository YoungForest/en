---
title: LeetCode weekly contest 130
date: 2019-03-31 12:43:43
description: Routine contest notes on binary-prefix remainders, base negative two, next-greater linked nodes and boundary-first enclave search.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/03/31/LeetCode-weekly-contest-130/
  en: https://youngforest.github.io/en/2019/03/31/LeetCode-weekly-contest-130/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-130/en-hero.webp" alt="Forest reaches a steeper practice path with a remainder carousel, negative-base balance, monotonic bead chain and boundary flood" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (5) |	Q4 (5)|
|--|--|--|--|--|--|--|--|
|258 / 5236|	YoungForest |	19|	0:57:19 |	0:06:23 |	0:25:41 |	0:36:25	|0:52:19(1) |

This contest was relatively easy. All four problems were routine, and without a Hard problem to separate the field, it came down to implementation speed. You had to finish within 50 minutes to get into the top 200.
I lost some time on the second problem, and for the last problem my first idea was muddled, so I took a bit of a detour.

## 1029. Binary Prefix Divisible By 5

Intuition:
Straightforward. Shift through the bits and check the remainder modulo 5. Pay attention to taking `current` modulo, because `A` can be very long.
Time complexity: O(N),
space complexity: O(N).

```cpp
class Solution {
public:
    vector<bool> prefixesDivBy5(vector<int>& A) {
        vector<bool> ret(A.size(), false);
        uint32_t current = 0;
        for (int i = 0; i < A.size(); i++) {
            current = (current << 1) % 10;
            current += A[i];
            if (current == 0 || current == 5)
                ret[i] = true;
        }
        
        return ret;
    }
};
```

## 1028. Convert to Base -2

Intuition:
Analogous to the base-2 solution. Keep dividing by 2.

```cpp
class Solution {
public:
    string baseNeg2(int N) {
        if (N == 0) return "0";
        string ret_reverse;
        bool flag = true; // 正
        while (N > 0) {
            if (N % 2 == 1) {
                ret_reverse.push_back('1');
                if (flag)
                    N /= 2;
                else
                    N = (N + 1) / 2;    // 此处要加一，因为最后一位的权重此时是-1. N下一步的迭代要把其补上
            } else {
                ret_reverse.push_back('0');
                N /= 2;
            }
            flag = !flag;
        }
        
        reverse(ret_reverse.begin(), ret_reverse.end());
        return ret_reverse;
    }
};
```

## 1030. Next Greater Node In Linked List

Intuition:
Monotonic stack.
Time complexity: O(N),
space complexity: O(N).

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> nextLargerNodes(ListNode* head) {
        // 单调栈
        vector<int> ret;
        stack<pair<int, int>> s; // index, value
        int index = 0;
        while (head) {
            while (!s.empty() && head->val > s.top().second) {
                auto current = s.top();
                s.pop();
                ret[current.first] = head->val;
            }
            ret.push_back(0);
            s.push({index, head->val});
            index++;
            head = head->next;
        }
        
        return ret;
    }
};
```

## 1031. Number of Enclaves

Intuition:
DFS. Start the search from the boundary.
Time complexity: O(N^2),
space complexity: O(N^2).

```cpp
class Solution {
    void dfs(vector<vector<int>>& A, int i, int j) {
        if (A[i][j] == 0 || A[i][j] == 2) return;
        A[i][j] = 2;
        vector<int> di {0, 1, 0, -1};
        vector<int> dj {1, 0, -1, 0};
        for (int k = 0; k < 4; k++) {
            int new_i = di[k] + i;
            int new_j = dj[k] + j;
            if (new_i >= 0 && new_j >= 0 && new_i < A.size() && new_j < A[0].size()) {
                dfs(A, new_i, new_j);
            }
        }
    }
public:
    int numEnclaves(vector<vector<int>>& A) {
        for (int j = 0; j < A[0].size() - 1; j++) {
            dfs(A, 0, j);
        }
        for (int i = 0; i < A.size() - 1; i++) {
            dfs(A, i, A[0].size() - 1);
        }
        for (int j = A[0].size() - 1; j > 0; j--) {
            dfs(A, A.size() - 1, j);
        }
        for (int i = A.size() - 1; i > 0; i--) {
            dfs(A, i, 0);
        }
        
        int ret = 0;
        for (int i = 0; i < A.size(); i++) {
            for (int j = 0; j < A[0].size(); j++) {
                if (A[i][j] == 1)
                    ret++;
            }
        }
        return ret;
    }
};
```


## Postscript

I have solved 319 / 969 problems on LeetCode, which feels like reaching a certain bottleneck. Right now I am going by acceptance rate, which usually means from easy to hard. The easy problems are already done, and the later ones will become harder and harder. At the beginning I could write one problem in 10 minutes and know the approach as soon as I saw it. Now I need half an hour, and each problem requires a while of thinking.
But this is a good sign. Only by working on problems that are difficult enough, yet still within my current ability, can I improve quickly.

Keep going, Forest!
