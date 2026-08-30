---
title: LeetCode weekly contest 127
date: 2019-03-10 10:32:00
description: Speed-contest notes on array negations, clumsy factorial, domino rotations and constructing a search tree from preorder.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/03/10/LeetCode-weekly-contest-127/
  en: https://youngforest.github.io/en/2019/03/10/LeetCode-weekly-contest-127/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-127/en-hero.webp" alt="Forest checks a corner-case gear while flipping beads, timing operation wheels, rotating color tiles and growing a preorder tree" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

The scores for this week's four problems were 4, 4, 5, and 6, so they should not be too hard. Keep going, Forest!

Because the problems were too simple, even though I finished 15 minutes early, my rank was still 912 / 4712. This contest was really easy, and it completely tested coding speed and familiarity. Whether you can get it bug-free on the first try matters a lot. If a corner case is wrong, debugging it takes a lot of time. I got one wrong answer on both problems 1 and 2, which cost me quite a bit of time.

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (4) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
|931 / 4059|	YoungForest |	19|	1:24:03|	0:24:49(1)|	0:44:58(1)|	1:05:12	|1:14:03|

## 1005. Maximize Sum Of Array After K Negations

A routine warm-up problem.

Intuition:
If there are negative numbers, flip the smallest negative numbers first;
if there are no negative numbers but there is a zero, flip zero;
if there are only positive numbers and `K` is odd, flip the smallest positive number;
if `K` is even, do not flip.

Time complexity: O(N log N), because of sorting.
Space complexity: O(1).

```cpp
class Solution {
public:
    int largestSumAfterKNegations(vector<int>& A, int K) {
        sort(A.begin(), A.end(), less<int>());
        int i;
        for (i = 0; i < A.size() && K > 0 && A[i] < 0; ++i) {
            A[i] = -A[i];
            K--;
        }
        if (K > 0 && K % 2 == 1) {
            if (i > 0 && A[i-1] < A[i]) A[i-1] = -A[i-1];
            else A[i] = -A[i];
        }
        return accumulate(A.begin(), A.end(), 0, plus<int>());
    }
};
```

I got one Wrong Answer because when flipping the smallest positive number, I did not consider a smaller positive value that originally came from a negative number. That is why the line `if (i > 0 && A[i-1] < A[i]) A[i-1] = -A[i-1];` is needed.

Even for a warm-up problem, I still found some impressive code in the Discuss section. For example, an O(N) solution.
```cpp
class Solution {
    int partition(vector<int>& A, int low, int high) {
        int pivot = A[high];
        int placement = low;
        for (int i = low; i < high; ++i) {
            if (A[i] < pivot) {
                swap(A[i], A[placement]);
                placement++;
            }
        }
        swap(A[high], A[placement]);
        
        return placement;
    }
public:
    int largestSumAfterKNegations(vector<int>& A, int K) {
        int low = 0, high = A.size() - 1;
        int index;
        while (true) {
            index = partition(A, low, high);
            if (index < K - 1)  // why not K? Think the case A.size() == K
                low = index + 1;
            else if (index > K - 1)
                high = index - 1;
            else
                break;
        }
    
        for (int i = 0; i < index; i++) {
            if (A[i] < 0) {
                A[i] = -A[i];
                K--;
            }
        }
        
        return accumulate(A.begin(), A.end(), 0) - ((K%2) == 1 ? 2 * *min_element(A.begin(), A.end()) : 0);
    }
};
```

Time complexity: O(N) on average.
Space complexity: O(1).

If we also want the worst case to be O(N), we can use randomized partition.

## 1006. Clumsy Factorial

In my first submission, I misunderstood the precedence and lowered the priority of `-`, then wrote a foolish recursive solution. The result was that negative times negative became positive, which was obviously wrong.
Intuition:
Compute directly according to the definition of "Clumsy Factorial".

```cpp
class Solution {
public:
    int clumsy(int N) {
        if (N == 4) {
            return 4*3 / 2 + 1;
        } else if (N == 3) {
            return 3*2 / 1;
        } else if (N == 2) {
            return 2 * 1;
        } else if (N == 1) {
            return 1;
        } else {
            unsigned int result = N * (N - 1) / (N - 2) + (N - 3);
            int i;
            for (i = 4; i + 4 <= N; i+=4) {
                result += - (N - i) * (N - i - 1) / (N - i - 2) + (N - i - 3);
            }
            if (N - i == 3) result += -3*2/1;
            else if (N - i == 2) result += -2*1;
            else if (N - i == 1) result += -1;
            else;
            return result;
        }
    }
};
```

Time complexity: O(N),
space complexity: O(1).

The Discuss section had a mathematical O(1) optimization, and I have to include the [original link](https://leetcode.com/problems/clumsy-factorial/discuss/252279/You-never-think-of-this-amazing-O(1)-solution!!!). It is truly ridiculous in the best way.


## 1007. Minimum Domino Rotations For Equal Row
Intuition:
To make the whole row of `A` or `B` equal, there can be 12 final states: `A` is 1, `A` is 2, `A` is 3, `A` is 4, ..., `B` is 6.
One pass over the dominoes gives the number of flips needed to reach these 12 states, using -1 to represent an impossible state.

```cpp
class Solution {
public:
    int minDominoRotations(vector<int>& A, vector<int>& B) {
        if (A.size() != B.size()) return -1;
        vector<int> states(12, 0);
        
        for (int i = 0; i < A.size(); i++) {
            int count = 0;
            for (int j = 0; j < 6; j++) {
                if (j + 1 == A[i] && states[j] >= 0)
                    states[j] = states[j];
                else if (j + 1 == B[i] && states[j] >= 0)
                    states[j] ++;
                else {
                    states[j] = -1;
                    count++;
                }
            }
            for (int j = 0; j < 6; j++) {
                if (j + 1 == B[i] && states[j + 6] >= 0)
                    states[j + 6] = states[j + 6];
                else if (j + 1 == A[i] && states[j + 6] >= 0)
                    states[j + 6] ++;
                else {
                    states[j + 6] = -1;
                    count++;
                }
            }
            
            if (count == 12) return -1;
        }
        
        int result = -1;
        for (int i = 0; i < 12; i++) {
            if (states[i] >= 0)
                result = result >= 0 ? min(result, states[i]) : states[i];
        }
        return result;
    }
};
```

## 1008. Construct Binary Search Tree from Preorder Traversal

Intuition:
Tree problems are usually solved with recursion. The key to this problem is understanding **Binary Search Tree** and **Preorder Traversal**.

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
    TreeNode* dfs(vector<int>& preorder, int begin, int end) {
        if (begin > end) return nullptr;
        if (begin == end) return new TreeNode(preorder[begin]);
        TreeNode* root = new TreeNode(preorder[begin]);
        int mid;
        for (mid = begin + 1; mid <= end && preorder[mid] < root->val; mid++);
        root->left = dfs(preorder, begin + 1, mid - 1);
        root->right = dfs(preorder, mid, end);
        return root;
    }
public:
    TreeNode* bstFromPreorder(vector<int>& preorder) {
        return dfs(preorder, 0, preorder.size() - 1);
    }
};
```

Actually, an O(N) solution exists. That is, we do not need to find the boundary between the left and right subtrees; we only need to keep expanding the tree.
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
    int i = 0;
    TreeNode* dfs(vector<int>& preorder, int bound) {
        if (i == preorder.size() || preorder[i] > bound) return nullptr;
        TreeNode* root = new TreeNode(preorder[i++]);
        root->left = dfs(preorder, root->val);
        root->right = dfs(preorder, bound);
        return root;
    }
public:
    TreeNode* bstFromPreorder(vector<int>& preorder) {
        return dfs(preorder, numeric_limits<int>::max());
    }
};
```

## Postscript

Recently, because I have also been busy with a project at the China Institute of Water Resources, I have slackened a bit on problem-solving. I need to reflect on myself and recognize the current situation again. If I want to go to a foreign company, problem-solving really is just the basic skill. Borrowing the concept from martial arts novels, "data structures and algorithms are a programmer's internal strength." If I cannot even persist with something this simple, then my life will probably amount to nothing.

Plan: even though I come back exhausted every evening, I still need to spend one hour on problem-solving and CS fundamentals, half an hour running, and half an hour on English. I will check in on Douban every day, and I hope everyone will supervise me.
