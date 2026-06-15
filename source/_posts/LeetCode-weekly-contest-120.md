---
title: LeetCode weekly contest 120
date: 2019-01-20 11:51:13
tags:
- Competitive Programming
categories:
- LeetCode
---

This week, I joined the weekly contest together with my good friend "Female Voice Male". Competing with a classmate still brings quite a bit of pressure. I have been practicing algorithm problems for half a year, while he is still a beginner. If I lost in the end, that would be embarrassing. Fortunately, the result was acceptable, and I did not embarrass myself. I ACed all problems with 10 minutes left, and every problem passed on the first try, so I was slightly ahead. I have to say that this contest's problems were much easier than previous ones. My previous level had stayed at solving only two problems with a ranking around 800, while this time my ranking was 356 / 3870. From the ranking, there was some progress.

Below I share the ideas for the four problems.

## 977. Squares of a Sorted Array

Given an array, return the squared values of its elements, sorted. Direct brute force is enough.

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& A) {
        vector<int> square;
        
        for (auto a: A) {
            square.push_back(a * a);
        }
        
        sort(square.begin(), square.end());
        
        return square;
    }
};
```

Time complexity: O(n log n), because there is a sort.
Space complexity: O(n), because a new array needs to be returned. Other than that, no extra space is used. Of course, you can also use the space occupied by the input array, which is trickier but not very meaningful.

After reading the Solution, I was surprised to find that there is an O(n) approach. Looking carefully, I realized I had not noticed that array `A` itself is already sorted. The problem can be transformed into merging two sorted arrays.

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& A) {
        vector<int> square;
        
        int j = 0;
        for (; j < A.size() && A[j] < 0; j++) ;
        
        int i = j - 1;
        while (i >= 0 && j < A.size()) {
            if (A[i]*A[i] < A[j]*A[j]) {
                square.push_back(A[i]*A[i]);
                i--;
            }
            else {
                square.push_back(A[j]*A[j]);
                j++;
            }
        }

        while (i >= 0) {
            square.push_back(A[i]*A[i]);
            i--;
        }
        
        while (j < A.size()) {
            square.push_back(A[j]*A[j]);
            j++;
        }
        
        return square;
    }
};
```

## 978. Longest Turbulent Subarray

Given an array, return the maximum length of a "turbulent subarray". A "turbulent subarray" means that the sign of each adjacent pair's comparison alternates, such as `[1, 2, 1, 2, 1]`. Plotted on a coordinate chart, it looks like a wave.

Idea: traverse the array once. If the comparison sign of the new adjacent pair is opposite to the previous pair's sign, then `current turbulent subarray length += 1`; otherwise, reset `current turbulent subarray length` to 2. During traversal, update `longest turbulent subarray length` according to `current turbulent subarray length`.

```cpp
class Solution {
public:
    int maxTurbulenceSize(vector<int>& A) {
        int max_length = 1;
        // 1 down, -1 up
        int last_state = 0;
        int current_length = 2;
        
        for (int i = 0; i < A.size() - 1; i++) {
            int state = 0;
            if (A[i] > A[i + 1]) {
                state = 1;
            } else if (A[i] < A[i + 1]) {
                state = -1;
            } else {
                state = 0;
            }
            if (state == -last_state && state != 0) {
                current_length++;
            } else {
                current_length = 2;
            }
            max_length = max(max_length, current_length);
            
            last_state = state;
        }
        
        return max_length;
    }
};
```
Time complexity: O(n), only one traversal of the array is needed;
space complexity: O(1), only constant extra space is used.



## 979. Distribute Coins in Binary Tree

Intuition: when encountering a tree problem, the first thing to think of is recursion and DFS. The hardest part of writing DFS directly is determining the parameters and return value, that is, clarifying the relationship between the subtree and the root. In this problem, the relationship between a subtree and the root is coin transfer, either from the subtree to the root or from the root to the subtree. So only one return value is needed, representing this transfer.

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
private:
    int move = 0;
    // 把多余的给parent，问parent要不够的, positive: surplus, negative: demand
    int recurse(TreeNode* root) {
        if (!root) return 0;
        int surplus = 0;
        
        int left = recurse(root->left);
        int right = recurse(root->right);
        
        surplus = left + right + root->val - 1;
        
        move += abs(surplus);
        
        return surplus;
    }
public:
    int distributeCoins(TreeNode* root) {
        recurse(root);
        
        return move;
    }
};
```

Time complexity: O(n), where n is the number of nodes, because each node is called once.
Space complexity: average O(log n), namely the tree depth.

## 980. Unique Paths III

Intuition: to find the number of paths, the most direct brute-force approach is DFS with backtracking. Since the problem scale is small, `1 <= grid.length * grid[0].length <= 20`, it can still AC. The exciting part was that I ACed this last problem with 10 minutes left before the contest ended.

```cpp
class Solution {
private:
    int path = 0;
    void dfs(vector<vector<int>>& grid, vector<vector<bool>> &visit, int i, int j) {
        // cout << "dfs: " << i << "," << j << endl;
        if (grid[i][j] == 2) {
            int true_all = true;
            for (int xi = 0; xi < grid.size(); xi++) {
                for (int xj = 0; xj < grid[xi].size(); xj++) {
                    if (grid[xi][xj] == 0 && visit[xi][xj] == false) {
                        true_all = false;
                        goto end_of_outer_loop;
                    }
                }
            }
            end_of_outer_loop:
            if (true_all) {
                // cout << "find a path!" << endl;
                path++;   
            }
            return;
        }
        visit[i][j] = true;
        if (i > 0) {
            int new_i = i - 1;
            int new_j = j;
            if (visit[new_i][new_j] == false && (grid[new_i][new_j] == 0 || grid[new_i][new_j] == 2))
                dfs(grid, visit, new_i, new_j);
        }
        if (j > 0) {
            int new_i = i;
            int new_j = j - 1;
            if (visit[new_i][new_j] == false && (grid[new_i][new_j] == 0 || grid[new_i][new_j] == 2))
                dfs(grid, visit, new_i, new_j);
        }
        if (i < grid.size() - 1) {
            int new_i = i + 1;
            int new_j = j;
            if (visit[new_i][new_j] == false && (grid[new_i][new_j] == 0 || grid[new_i][new_j] == 2))
                dfs(grid, visit, new_i, new_j);
        }
        if (j < grid[0].size() - 1) {
            int new_i = i;
            int new_j = j + 1;
            if (visit[new_i][new_j] == false && (grid[new_i][new_j] == 0 || grid[new_i][new_j] == 2))
                dfs(grid, visit, new_i, new_j);
        }
        visit[i][j] = false;
    }
public:
    int uniquePathsIII(vector<vector<int>>& grid) {
        vector<vector<bool>> visit(grid.size(), vector<bool> (grid[0].size(), false));
        
        int i, j;
        for (i = 0; i < grid.size(); i++) {
            for (j = 0; j < grid[0].size(); j++) {
                if (grid[i][j] == 1) {
                    goto find_begin_square;
                }
            }
        }
        find_begin_square:
        
        
        // cout << "begin : " << i << "," << j << endl;
        dfs(grid, visit, i, j);
        
        return path;
    }
};
```

Time complexity: O(4^n), where n is the number of grid cells, because each step can go in four directions and backtracking is necessarily exponential.
Space complexity: O(n), the deepest recursion depth of DFS.

