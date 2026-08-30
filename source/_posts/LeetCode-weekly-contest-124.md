---
title: LeetCode weekly contest 124
date: 2019-02-17 15:11:48
description: "Four contest solutions cover cousin nodes, multi-source BFS for rotting oranges, greedy fixed-window flips, and square-sum permutations."
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/02/17/LeetCode-weekly-contest-124/
  en: https://youngforest.github.io/en/2019/02/17/LeetCode-weekly-contest-124/
---
Today was my first LeetCode weekly contest after work started again. I solved three problems and ranked 772 / 4174. Seeing my rank drop from 200+ to 700+ each time felt pretty disappointing. I think the reasons for the drop are: 1. Ranking around 200 was a case where both my state and luck were relatively good; most of the time before, I was also around 700. 2. Although the third problem was Hard, my final submission TLEed. I think if I had had another half hour, I could have ACed it. The reason I did not have enough time later was directly related to spending too much time debugging problems 2 and 4. I am still not familiar enough with many implementations, such as BFS and backtracking, and cannot write them flexibly from memory. Even when solving the fourth problem, I still had to look up C++ APIs on the spot, so my familiarity with the language is also insufficient.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-124/en-hero.webp" alt="A greenhouse tree holds cousin oranges above a spreading tile orchard while a reversible ribbon leads through glowing square arches" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 993. Cousins in Binary Tree

Find cousins in a binary tree. "Cousins" means two nodes are on the same level but have different parents.

Intuition: recursively find each node's parent and depth, then compare whether the two nodes have different parents and the same depth. In other words, run DFS over the binary tree twice.

Time complexity: O(N),
space complexity: average O(Log N), worst case O(N), because a recursion stack is needed.

During the Spring Festival holiday, I read LeetCode's recursion card, [Introduction to Algorithms - Recursion I](https://leetcode.com/explore/learn/card/recursion-i/), so I finished this warm-up problem in 10 minutes.

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
    // parent, depth
    pair<int, int> findNode(TreeNode* root, int value, int parent, int depth) {
        if (!root) return {0, depth + 1};
        if (root->val == value) return {parent, depth + 1};
        auto left = findNode(root->left, value, root->val, depth + 1);
        auto right = findNode(root->right, value, root->val, depth + 1);
        if (left.first != 0) return left;
        if (right.first != 0) return right;
        return {0, depth + 1};
    }
public:
    bool isCousins(TreeNode* root, int x, int y) {
        auto xNode = findNode(root, x, 0, 0);
        auto yNode = findNode(root, y, 0, 0);
        if (xNode.first != yNode.first && xNode.second == yNode.second)
            return true;
        return false;
    }
};
```

## 994. Rotting Oranges

Given a grid, each cell has one of three states:
- empty
- fresh orange
- rotten orange

Every day, rotten oranges turn adjacent fresh oranges into rotten oranges. Find the number of days until all oranges become rotten. If that is impossible, return -1.

Intuition:
1. Run BFS from each rotten orange, infecting fresh oranges one layer at a time and updating the day on which each fresh orange rots. Since we need the shortest distance from a fresh orange to a rotten orange, BFS is required.
2. Check the rot day for all fresh oranges. If any orange still has an infinite day, return -1.

```cpp
class Solution {
    void bfs(int i, int j, vector<vector<int>>& grid, vector<vector<bool>>& visited, vector<vector<int>> &minDays, int depth) {
        vector<int> di = {1, 0, -1, 0};
        vector<int> dj = {0, 1, 0, -1};
        
        queue<tuple<int, int, int>> q;
        q.push({i ,j, depth});
        
        while (!q.empty()) {
            tuple<int, int, int> current = q.front();
            q.pop();
            
            i = get<0>(current);
            j = get<1>(current);
            depth = get<2>(current);
            
            visited[i][j] = true;
            minDays[i][j] = min(minDays[i][j], depth);
            
            for (int k = 0; k < di.size(); k++) {
                int new_i = i + di[k];
                int new_j = j + dj[k];
                if (new_i < grid.size() && new_i >= 0 && new_j < grid[0].size() && new_j >= 0 && grid[new_i][new_j] == 1 && visited[new_i][new_j] == false)
                    q.push({new_i, new_j, depth + 1});
            }
        }
        
    }
public:
    int orangesRotting(vector<vector<int>>& grid) {
        vector<vector<int>> minDays(grid.size(), vector<int> (grid[0].size(), numeric_limits<int>::max()));
        for (int i = 0; i < grid.size(); ++i) {
            for (int j = 0; j < grid[i].size(); ++j) {
                if (grid[i][j] != 1)
                    minDays[i][j] = 0;
            }
        }
        
        for (int i = 0; i < grid.size(); ++i) {
            for (int j = 0; j < grid[i].size(); ++j) {
                if (grid[i][j] == 2) {
                    vector<vector<bool>> visited(grid.size(), vector<bool> (grid[0].size(), false));
                    bfs(i, j, grid, visited, minDays, 0);
                }
            }
        }
        
        int result = 0;
        for (int i = 0; i < grid.size(); ++i) {
            for (int j = 0; j < grid[i].size(); ++j) {
                result = max(result, minDays[i][j]);
            }
        }
        
        return result == numeric_limits<int>::max() ? -1 : result;
    }
};
```

The official Solution also uses BFS. The clever point is that by recording each node's depth and putting all rotten oranges into the queue at the start, each node only needs to be visited once. It is clearer and has lower time complexity. It is better than the code I wrote.

## 995. Minimum Number of K Consecutive Bit Flips

Given an array `A` containing only two states, 0 and 1. Given `K`, the operation `flip` can invert a subarray of length `K`.
Find the minimum number of flips needed to turn all of `A` into 1.
If it is impossible, return -1.

Intuition:
If all values must eventually become 1, then if the first value is 0, we must flip the subarray starting from the first position.
Following this logic, we must flip every subarray whose first current value is 0 from left to right. If there is still a 0 among the final `K` positions that cannot be covered, then it is impossible to turn all values into 1.
This is a Greedy algorithm.

Time complexity: O(N * K), which timed out.
Space complexity: O(1).

```cpp
class Solution {
public:
    int minKBitFlips(vector<int>& A, int K) {
        int result = 0;
        int i;
        for (i = 0; i < A.size(); i++) {
            if (A[i] == 0) {
                if (i + K > A.size()) {
                    break;
                }
                // cout << "flip A: " << i << endl;
                for (int j = 0; j < K; j++) {
                    if (A[i + j] == 0) {
                        A[i + j] = 1;
                    } else {
                        A[i + j] = 0;
                    }
                }
                result++;
            }
        }
        if (i < A.size())
            return -1;
        return result;
    }
};
```

Actually, if we think carefully, we do not need to perform the flip operation itself. We only need to record the number of flips. So the `* K` factor in time is not necessary.
But if we do not actually flip, how do we know whether a position is a 0 that needs flipping or a 1 that needs flipping?
Each time, we record the end position of the flip. Before that end position, the subarray of length `K` has been flipped, and two flip operations can cancel each other out directly.

```cpp
class Solution {
public:
    int minKBitFlips(vector<int>& A, int K) {
        int result = 0;
        vector<int> flip(A.size(), 0);
        int i;
        int zero = 0;   // 初始情况，0该翻转
        for (i = 0; i < A.size(); i++) {
            zero ^= flip[i];    // 走出结束位置，回退翻转（相当于再次翻转）
            if (A[i] == zero) {
                if (i + K > A.size()) {
                    break;
                }
                result++;
                zero ^= 1;  // 进入开始位置，进行翻转
                if (i + K < A.size())
                    flip[i + K] ^= 1;   // 标定结束位置
            }
        }
        if (i < A.size())
            return -1;
        return result;
    }
};
```

## 996. Number of Squareful Arrays

If the sum of any two adjacent numbers in an array is a square, then the array is called a Squareful Array.
Given an array `A`, find how many permutations of `A` are Squareful Arrays.

Intuition:
Use backtracking. The pruning condition is that the sum of the last two adjacent numbers is not a Perfect Square.

```cpp
class Solution {
    int count = 0;
    
    bool isPerfectSquare(long double x) 
    {   
      // Find floating point value of  
      // square root of x. 
      long double sr = sqrt(x); 

      // If square root is an integer 
      return ((sr - floor(sr)) == 0); 
    } 
    
    void backtracking(vector<int>& current, multiset<int>& condidates) {
        int n = current.size();
        if (condidates.size() == 0) {
            count++;
            return;
        }
        for (auto i = condidates.begin(); i != condidates.end();) {
            int c = *i;
            int sum_;
            if (n > 0) {
                sum_ = current[n - 1] + c;
                // cout << sum_ << " " << isPerfectSquare((long double) sum_) << endl;
                if (isPerfectSquare((long double) sum_)) {
                    current.push_back(c);
                    auto it = condidates.find(c);
                    condidates.erase(it);
                    backtracking(current, condidates);
                    current.pop_back();
                    condidates.insert(c);
                }
            } else {
                current.push_back(c);
                auto it = condidates.find(c);
                condidates.erase(it);
                backtracking(current, condidates);
                current.pop_back();
                condidates.insert(c);
            }
            i = condidates.upper_bound(c);
        }

    }
public:
    int numSquarefulPerms(vector<int>& A) {
        // backtracking
        // 剪枝条件：相邻的pair和不是平方数
        vector<int> current;
        multiset<int> condidates;
        condidates.insert(A.begin(), A.end());
        backtracking(current, condidates);
        
        return count;
    }
};
```


## Postscript

<!-- This was my first week interning at SenseTime. The internship workload at SenseTime is heavy; after a full day, my eyes hurt badly. I need to adjust my pace later and pay attention to balancing work and rest. Across several internship experiences, SenseTime is the most tiring, but the expected gain should also be larger. The benefits of interning at SenseTime are:
1. There are many Beihang classmates and seniors, and the technical communication atmosphere is smooth. Many times when I ran into difficulties, they came to my desk to help solve the problem together. I am a thin-skinned person and usually worry about being looked down upon. But here, I am gradually learning that when I encounter a problem, I should ask coworkers instead of fighting alone. That makes both work and growth more efficient.
2. It is close to Beihang, in Tsinghua Science Park. Kuaishou's previous Tongfang Science Park office was also close, so I could bike back and forth every day and return to school at any time if something urgent came up. But now Kuaishou has moved to Xierqi; otherwise, going back to Kuaishou for a second round would also have been good.
3. Besides development, there is also some research work. That can connect with the lab's research and reduce the burden. The current research direction is model quantization. I have just started and can invest more energy in it later. -->

I have not yet re-entered a learning state or restored my study habits. Returning to an ambitious state is difficult. I must learn to cherish it again and again, and I must not fall back into a comfortable state. I know that a comfortable state is not good, and getting back into an ambitious state is even harder.
I need to study and exercise regularly every day, complete my New Year goals, and pursue my original dream.
