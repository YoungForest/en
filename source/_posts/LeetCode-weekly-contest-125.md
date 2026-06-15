---
title: LeetCode weekly contest 125
date: 2019-02-24 11:11:14
tags:
- Competitive Programming
categories:
- LeetCode
---

This weekend was insanely busy, and I paid the price for my procrastination and laziness. Everything piled up together, and Sunday had a ridiculous number of deadlines. At 10 a.m. I had a meeting with my advisor to discuss how to prepare the make-up exam for the undergraduate Computer Organization course. By the time I got back to the lab after the meeting, the contest had already started. After a brief hesitation over whether to join the contest as planned or first finish preparing the make-up exam, I started this week's weekly contest. It is one of the few things I have kept doing over the past two months, and continuing it is no longer only about improving my algorithm skills. It is also a huge encouragement for my confidence in taking control of my own life.

<!-- more -->

This week's weekly contest was relatively easy. Although I was 10 minutes late, I still finished all four problems 30 minutes early. In particular, the fourth problem was worth 8 points and marked Hard, but I solved it quickly. I think its difficulty was at most Medium.

Result:

| Rank |	Name |	Score |	Finish Time | 	Q1 (4) |	Q2 (4) |	Q3 (6) |	Q4 (8)|
|--|--|--|--|--|--|--|--|
| 284 / 4238	| YoungForest | 	22 |	1:08:40 |	0:18:40 |	0:31:07  WA(1) |	0:39:39 |	1:03:40 |

## 997. Find the Town Judge

Use two hashmaps: one records how many people each person trusts, and the other records how many people trust each person.

```cpp
class Solution {
public:
    int findJudge(int N, vector<vector<int>>& trust) {
        unordered_map<int, int> vote;
        unordered_map<int, int> untrust;
        for (vector<int> & p : trust) {
            vote[p[1]]++;
            untrust[p[0]]++;
        }
        
        for (int i = 1; i <= N; i++) {
            if (vote[i] == N - 1 && untrust[i] == 0) return i;
        }
        
        return -1;
    }
};
```

## 999. Available Captures for Rook

This fully matches Easy difficulty. Directly simulate the rook, whose movement should be similar to the rook in Chinese chess, walking step by step in four directions.
In the words of someone from Discuss: search and capture.

```cpp
class Solution {
public:
    int numRookCaptures(vector<vector<char>>& board) {
        int rock_i, rock_j;
        for (int i = 0; i < board.size(); i++) {
            for (int j = 0; j < board[0].size(); j++) {
                if (board[i][j] == 'R') {
                    rock_i = i;
                    rock_j = j;
                    break;
                }
            }
        }
        vector<int> di = {0, 1, 0, -1};
        vector<int> dj = {1, 0, -1, 0};
        int result = 0;
        for (int k = 0; k < di.size(); k++) {
            for (int i = rock_i, j = rock_j; i >= 0 && i < board.size() && j >= 0 && j < board[0].size(); i += di[k], j += dj[k]) {
                char current = board[i][j];
                if (current == 'R') continue;
                else if (current == '.') continue;
                else if (current == 'p') {
                    result++;
                    break;
                }
                else if (current == 'B') {
                    break;
                }
                else return -1; 
            }
        }
        
        return result;
    }
};
```

## 998. Maximum Binary Tree II

The key to this problem is **understanding what the so-called Maximum Binary Tree means**.
Its definition itself is recursive: given an array `A`, the maximum value in `A` is the root node; the subarray to the left of the maximum value forms the left subtree, and the subarray to the right forms the right subtree.

This problem gives a Maximum Binary Tree but does not tell you the original array. It then gives one more value and asks for the Maximum Binary Tree constructed after appending this value to the original array.

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
    TreeNode* insertIntoMaxTree(TreeNode* root, int val) {
        if (!root) return new TreeNode(val);
        if (val > root->val) {
            TreeNode *append = new TreeNode(val);
            append->left = root;
            return append;
        } else {
            root->right = insertIntoMaxTree(root->right, val);
            return root;
        }
    }
};
```

## 1001. Grid Illumination

Wow! LeetCode problem numbers have passed 1000. Although the total number of problems is still under 1k, reaching it is only a matter of time.
There are 949 algorithm problems now, and I have only done 210. Come on, Forest!

The idea for this problem is very direct. Once you understand the statement, just search. The key is search efficiency.
From the constraints:
>1 <= N <= 10^9
>
>0 <= lamps.length <= 20000
>
>0 <= queries.length <= 20000
>
>lamps[i].length == queries[i].length == 2

we know that search is best implemented with a hashtable for maximum efficiency. However, C++'s default `pair` is not hashable. Although implementing a hashable pair yourself can further optimize efficiency, I was lazy here and directly used `set` instead of `unordered_set`. It still ACed. So happy. I finished the contest 30 minutes early, and my rank returned to the top 300 again. Keep it up and stay within the top 300.

```cpp
class Solution {
public:
    vector<int> gridIllumination(int N, vector<vector<int>>& lamps, vector<vector<int>>& queries) {
        set<pair<int, int>> lamps_on;
        unordered_map<int, int> lamps_x; // x, count
        unordered_map<int, int> lamps_y; // y, count
        unordered_map<int, int> lamps_x_sub_y; // x - y, count
        unordered_map<int, int> lamps_x_add_y; // x + y, count
        for (vector<int>& lamp : lamps) {
            lamps_on.insert({lamp[0], lamp[1]});
            lamps_x[lamp[0]]++;
            lamps_y[lamp[1]]++;
            lamps_x_sub_y[lamp[0] - lamp[1]]++;
            lamps_x_add_y[lamp[0] + lamp[1]]++;
        }
        
        vector<int> result;
        vector<int> di = {-1, 0, 1};
        vector<int> dj = {-1, 0, 1};
        for (vector<int>& q : queries) {
            if (lamps_x[q[0]] > 0 || lamps_y[q[1]] > 0 || lamps_x_sub_y[q[0] - q[1]] > 0 || lamps_x_add_y[q[0] + q[1]] > 0)
                result.push_back(1);
            else
                result.push_back(0);
            
            for (int i : di) {
                for (int j : dj) {
                    int x = q[0] + i;
                    int y = q[1] + j;
                    if (lamps_on.find({x, y}) != lamps_on.end()) {
                        lamps_on.erase({x, y});
                        lamps_x[x]--;
                        lamps_y[y]--;
                        lamps_x_sub_y[x - y]--;
                        lamps_x_add_y[x + y]--;
                    }
                }
            }
        }
        
        return result;
    }
};
```

Time complexity: O(max(M log N, N log N)), where N = `lamps.size()` and M = `queries.size()`. If using a hashable pair, the complexity can be further reduced to O(max(N, M)).
