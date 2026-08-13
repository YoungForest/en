---
title: LeetCode weekly contest 132
date: 2019-04-14 11:55:04
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/04/14/LeetCode-weekly-contest-132/
  en: https://youngforest.github.io/en/2019/04/14/LeetCode-weekly-contest-132/
---
This contest was not hard, but the code was not easy to implement. It was not easy to write bug-free in one pass. What it tested was using a programming language to handle complex logic and various unexpected cases. For example, in the third problem, when the previous `dp` is 0, the length should be updated to 2; otherwise it should be `dp + 1`. In the fourth problem, when looking for delimiters under various cases, how should it be handled if none is found?

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (5) |	Q3 (5) |	Q4 (5)|
|--|--|--|--|--|--|--|--|
|388 / 4765|	YoungForest |	24 | 	1:21:20	 | 0:15:54 | 0:30:16 |	0:38:05 |1:21:20 |
It probably required finishing within about 1 hour to enter the top 200.

For the fourth problem, I spent quite a lot of time debugging because of some boundary conditions. I analyzed why it took so long. It is still because I have not written enough code and am not sensitive enough to boundary conditions for variable updates. For example, when `string::find` does not find a result, how should the other coordinates be updated? I made the mistake that when nothing was found, it returned `npos(-1)`, and `current_find_index` should have been equal to `end`, instead of continuing to add the delimiter length on top of -1.

## 1025. Divisor Game

A pattern-finding problem.
Intution:
DP. Solution(N) = true if any Solution(N's divisor) is false, else false.
Time complexity: O(N^2),
Space complexity: O(N).

```cpp
class Solution {
public:
    bool divisorGame(int N) {
        // 1, false
        // 2, 1 true
        // 3, false
        // 4, 1 true
        // 5, 1 false
        // 6. 3, true
        // 7, 1, false
        // 8, 1, true
        // 9, false
        vector<bool> dp(1001, false);
        dp[1] = false;
        for (int i = 2; i <= N; i++) {
            for (int j = 1; j < i; j++) {
                if (i % j == 0 && dp[i - j] == false) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[N];
    }
};
```

After printing the results from 1 to 9, I found an impressive pattern. I tried it, and one line of code solved it.
It can be proved by mathematical induction:
Premise:
N is odd, false;
N is even, true.
1. If N is even, take x=1, and N-1 is false. Therefore N is True.
2. If N is odd, all its factors must also be odd, so N-x must be even, true. Therefore N is False.

Time complexity: O(1),
Space complexity: O(1).
```cpp
class Solution {
public:
    bool divisorGame(int N) {
        // 1, false
        // 2, 1 true
        // 3, false
        // 4, 1 true
        // 5, 1 false
        // 6. 3, true
        // 7, 1, false
        // 8, 1, true
        // 9, false
        return N % 2 != 1;
    }
};
```

## 1026. Maximum Difference Between Node and Ancestor

Intution:
Recursion. Since a possible difference may be `| root->val - the minimum value in the left subtree |`, or `| root->val - the maximum value in the left subtree |`, or subtracting the maximum/minimum value in the right subtree, or the larger Maximum Difference from the left and right subtrees.
So the recursive function returns three values: Maximum Difference, the minimum value in the tree, and the maximum value in the tree.

Time complexity: O(N), every node is recursively called once.
Space complexity: O(N), the height of the tree, O(log N) on average and O(N) in the worst case.

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
    // max_different, max, min
    tuple<int, int, int> dfs(TreeNode* root) {
        if (!root) {
            return {0, -1, 100001};
        }
        auto l = dfs(root->left);
        auto r = dfs(root->right);
        return {
            max({get<0>(l), get<0>(r), get<1>(l) == -1 ? -1 : abs(root->val - get<1>(l)), get<1>(r) == -1 ? -1 : abs(root->val - get<1>(r)), get<2>(l) == 100001 ? -1 : abs(root->val - get<2>(l)), get<2>(r) == 100001 ? -1 : abs(root->val - get<2>(r))}), 
            max({root->val, get<1>(l), get<1>(r)}), 
            min({root->val, get<2>(l), get<2>(r)})
        };
    }
public:
    int maxAncestorDiff(TreeNode* root) {
        auto ret = dfs(root);
        return get<0>(ret);
    }
};
```

## 1027. Longest Arithmetic Sequence
Intution:
DP.
Optimal substructure: `dp[a][b]` represents the length of a subsequence ending at coordinate a with difference b.
`dp[i][A[i] - A[j]] = dp[j][A[i] - A[j]] == 0 ? 2 : dp[j][A[i] - A[j]] + 1`.

Time complexity: O(N ^ 2 log N), which can be further reduced to O(N^2) with `unordered_map`.
Space complexity: O(N ^ 2). In the worst case, every pairwise difference is different.

```cpp
class Solution {
public:
    int longestArithSeqLength(vector<int>& A) {
        // n^2
        int ret = 0;
        vector<map<int, int>> dp(A.size()); // diff, length
        for (int i = 0; i < A.size(); ++i) {
            for (int j = 0; j < i; ++j) {
                dp[i][A[i] - A[j]] = dp[j][A[i] - A[j]] == 0 ? 2 : dp[j][A[i] - A[j]] + 1;
                ret = max(ret, dp[i][A[i] - A[j]]);
            }
        }
        
        return ret;
    }
};
```

## 1028. Recover a Tree From Preorder Traversal

Intution:
Recursively construct the tree. The hard part is string processing: how to implement it quickly and bug-free.

Time complexity: O(N * M), each node traverses the string at most once;
Space complexity: O(N), because we need to store the final tree, the recursion depth is at most N, and the recursive function itself consumes O(1) memory.

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
    // [Begin, end)
    TreeNode* dfs(string& S, int begin, int end, int depth) {
        // cout << begin << " " << end << " " << depth << endl;
        string delimiter(depth + 1, '-');
        int first_dash = S.find('-', begin);
        int val_end_index = min(end, first_dash == string::npos ? numeric_limits<int>::max() : first_dash);
        TreeNode* ret = new TreeNode(stoi(S.substr(begin, val_end_index - begin)));
        // 找到左右子树的分界点
        int left_begin = S.find(delimiter, begin);
        if (left_begin == string::npos || left_begin >= end) {
            return ret;
        }
        int current_find_index = left_begin + delimiter.size();
        // cout << "   " << current_find_index << endl;
        int left_end = S.find(delimiter, current_find_index);
        // cout << "   " << left_end << endl;
        current_find_index = (left_end == string::npos) ? end : left_end + delimiter.size();
        // cout << "   " << current_find_index << endl;
        while (left_end != string::npos && left_end < end && S[current_find_index] == '-') {
            while (S[current_find_index] == '-') ++current_find_index;
            // cout << "   " << current_find_index << endl;
            left_end = S.find(delimiter, current_find_index);
            // cout << "   " << left_end << endl;
            current_find_index = (left_end == string::npos) ? end : left_end + delimiter.size();
            // cout << "   " << current_find_index << endl;
        }
        ret->left = dfs(S, left_begin + delimiter.size(), left_end < end && left_end != string::npos ? left_end : end, depth + 1);
        int right_begin = current_find_index;
        if (right_begin < end)
            ret->right = dfs(S, right_begin, end, depth + 1);
        
        return ret;
    }
public:
    TreeNode* recoverFromPreorder(string S) {
        return dfs(S, 0, S.size(), 0);
    }
};
```

Although my own implementation has a simple idea, it is not easy to implement and is easy to get wrong. I also spent a long time debugging it.
So I went to the comments section and found a better solution: Iterative Stack.

Time complexity: O(M), one pass.
Space complexity: O(N).

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
public:
    TreeNode* recoverFromPreorder(string S) {
        stack<TreeNode*> s;
        int i = 0;
        while (i < S.size()) {
            int value = 0, level = 0;
            while (i < S.size() && S[i] == '-') {
                ++level;
                ++i;
            }
            while (i < S.size() && S[i] != '-') {
                value = value * 10 + S[i] - '0';
                ++i;
            }
            while (s.size() > level) {
                s.pop();
            }
            auto node = new TreeNode(value);
            if (!s.empty() && !s.top()->left) {
                s.top()->left = node;
            } else if (!s.empty()) {
                s.top()->right = node;
            }
            s.push(node);
        }
        TreeNode* ret;
        while (!s.empty()) {
            ret = s.top();
            s.pop();
        }
        return ret;
    }
};
```

## Afterword

Let me summarize recent life progress here every week.
1. I gave up on applying for Google Summer of Code. The reason is that after looking at some organizations I was interested in, I found that the proposal requirements were very high. Many of them were beyond what I could currently meet. On the other hand, my experience with open-source projects is indeed insufficient. The only presentable thing is a contribution I made to Apache/HAWQ last year. This year I can participate in more open-source activities to enrich my resume.
2. There is still no follow-up result for Microsoft's summer intern written test. The latest update in Action Center is still March 31.

Recently I have been reading Lippman's *Essential C++*. In the translator's preface, Hou Jie wrote a passage that I want to share with everyone.
> Some learners train themselves into iron-boned, steel-muscled people, able to cut through tropical jungles and chase across vast grasslands. Some learners are accustomed neither to large books, nor to rigorous style, nor to self-directed diligent study; they are flowers in a greenhouse, without the capital for self-reliance.

I do not know how everyone else is. When I reflect on my own learning process, I often "seek benefits and avoid harm," preferring simple superficial knowledge and stopping at a shallow taste when facing technologies that require large amounts of time to study deeply. I also know that, as a "programmer," the things that truly create distance from others are exactly the things that require settling down and studying deeply. Looking at other fields, it is actually the same. Since everyone is rational and everyone has inertia, simple good things will definitely attract crowds. Once too many people do them and the supply-demand balance is broken, the threshold will inevitably rise day by day.
Hou's words were a wake-up call. I also need to reflect on my learning state every day and correct mistaken thoughts and methods in time. Only then can I go farther and fly higher.
<!-- 3. The exchange program preparation materials still lack a transcript, and the graduate transcript requires the advisor's signature. I still have not found my advisor.
4. The SenseTime internship is as busy as ever. I learned quite a lot, but it is all superficial, with no deep or irreplaceable technology. My own depth of study is still not enough. -->
