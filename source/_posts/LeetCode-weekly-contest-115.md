---
title: LeetCode weekly contest 115
date: 2018-12-19 10:34:23
tags:
- Competitive Programming
categories:
- LeetCode
---

I have not participated in LeetCode weekly contests for a while. Recently, because I am preparing for Google's phone interview at the end of January, I need to pick algorithms back up again. Reviewing algorithm books is one part; the other part is preparing by solving problems. Since time is limited, LeetCode weekly contests are a good choice. The contest has a time limit, so it is closer to a real interview.
The weekly contest lasts one and a half hours, has four problems of different difficulty levels, and starts at 10:30 every weekend. Previously it was 9:30, maybe because of U.S. winter time, so it was delayed by one hour.
As before, I only completed two problems. For the third problem, I had some idea, later proven wrong. I glanced at the fourth problem and decisively gave up.
Below I share the ideas and solutions for the four problems. Of course, the latter two were completed afterward.

## 958. Check Completeness of a Binary Tree

Determine whether a tree is a complete binary tree.
For tree problems, recursion, BFS, and DFS are common tools. It is easy to see that BFS is the most suitable for this problem.
Once a node is found to be missing a child, set `no_child` to True. During the subsequent search, no other node is allowed to have children.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isCompleteTree(self, root: TreeNode) -> bool:
        no_child = False
        myqueue = collections.deque()
        if root:
            myqueue.append(root)
        
        while len(myqueue) > 0:
            node = myqueue.popleft()
            if no_child:
                if node.left != None or node.right != None:
                    return False
            else:
                if node.left != None:
                    myqueue.append(node.left)
                else:
                    no_child = True
                if node.right != None:
                    if no_child:
                        return False
                    else:
                        myqueue.append(node.right)
                else:
                    no_child = True
                    
        return True
```

Time complexity: O(n), where n is the number of nodes, because every node is traversed in BFS, at least for a complete tree; a non-complete tree can exit early.
Space complexity: O(n), because BFS uses a queue, and at most one level of nodes may be stored in the queue.

This problem feels more like Easy. Medium really overestimates it.

## 957. Prison Cells After N Days

This is a state-transition problem and can be directly simulated. The downside is that when `N` is too large, it will TLE.
It is easy to see that there are at most `2^6 = 64` cell states, so a cycle must appear during state transition. Therefore, as long as we save states seen before, when a state is encountered again, we can directly take modulo by the cycle length.
If you print out the state transitions, you can quickly find that 14 is a magic number: the cycle repeats every 14 days. So many implementations simply use mod 14 directly.

Time complexity: O(1),
space complexity: O(1).

```python
class Solution:
    def prisonAfterNDays(self, cells, N):
        """
        :type cells: List[int]
        :type N: int
        :rtype: List[int]
        """
        last_day = cells
        new_day = [0] * 8
        N = N % 14 + 14
        
        for i in range(1, 7):
            if last_day[i-1] == last_day[i+1]:
                new_day[i] = 1
            else:
                new_day[i] = 0
        new_day[0] = 0
        new_day[7] = 0
        last_day[0] = 0
        last_day[7] = 0
        last_day, new_day = new_day, last_day
        
        for _ in range(1, N):
            for i in range(1, 7):
                if last_day[i-1] == last_day[i+1]:
                    new_day[i] = 1
                else:
                    new_day[i] = 0
            last_day, new_day = new_day, last_day
            
        return last_day
```

## 959. Regions Cut By Slashes

The key to the problem is how many connected regions are divided out. At first, I wanted to use coloring, but later I got confused by the coloring order. I wrote 100 lines of code and still could not solve all cases correctly. In fact, DFS/BFS coloring is also a correct solution.
After reading the official Solution, I found that Union-Find is the right tool. The last part of Chapter 1 in *Algorithms, Fourth Edition* also focuses on Union-Find, which is perfect for solving this kind of connectivity problem. But because I had read Union-Find a long time ago and almost never used it in normal problem solving, I did not think of how conveniently it can solve similar connectivity problems.

Here I recommend *Algorithms, Fourth Edition* again. It is genuinely useful for interviews and improving algorithm skills. The more you read it, the more you benefit.

```python
class Solution:
    def regionsBySlashes(self, grid: List[str]) -> int:
        class UF:
            # quick union implement
            def __init__(self, size):
                self.parents = [i for i in range(size)]
                self.count = size
                
            def find(self, x):
                if self.parents[x] == x:
                    return x
                else:
                    self.parents[x] = self.find(self.parents[x])
                    return self.parents[x]
            
            def union(self, x, y):
                i = self.find(x)
                j = self.find(y)
                
                if i == j: return # already unioned
                
                self.parents[i] = j
                self.count -= 1
                
        uf = UF(4*len(grid)*len(grid[0]))
        
        for row, value in enumerate(grid):
            for column, char in enumerate(value):
                base = (row * len(grid[0]) + column) * 4
                if char == '\\':
                    uf.union(base, base + 2)
                    uf.union(base + 1, base + 3)
                elif char == '/':
                    uf.union(base, base + 1)
                    uf.union(base + 2, base + 3)
                elif char == ' ':
                    uf.union(base, base + 1)
                    uf.union(base, base + 2)
                    uf.union(base, base + 3)
                else:
                    pass
                if row > 0:
                    base_up = ((row - 1) * len(grid[0]) + column) * 4
                    uf.union(base, base_up + 3)
                if row < len(grid) - 1:
                    base_down = ((row + 1) * len(grid[0]) + column) * 4
                    uf.union(base + 3, base_down)
                if column > 0:
                    base_left = base - 4
                    uf.union(base + 1, base_left + 2)
                if column < len(grid[0]) - 1:
                    base_right = base + 4
                    uf.union(base + 2, base_right + 1)
                    
        return uf.count
```

## 960. Delete Columns to Make Sorted III

This is a typical problem where Dynamic Programming is useful. Because I ran out of time, I finally understood it only after reading the Solution.
Unfortunately, *Algorithms, Fourth Edition* does not cover DP, such an important concept. If you want to study DP systematically, read *Introduction to Algorithms* instead, although I have never finished it and have always only picked selected parts.

Let's follow *Introduction to Algorithms* step by step:
the four steps for solving a dynamic programming problem:
1. Characterize the structure of an optimal solution
2. Recursively define the value of an optimal solution
3. Compute the value of an optimal solution
4. Construct an optimal solution from computed information

When should we look for a Dynamic Programming solution to a problem?
1. Optimal substructure
2. Overlapping subproblems

First, minimum deleted columns = maximum retained columns, so we use maximum retained columns as the optimization target.
1. Optimal substructure: based on the discussion of optimal substructure in Section 15.3 of *Introduction to Algorithms*, the smaller and simpler the subproblem space, the better. So we choose the subproblem of `dp[i]` as `dp[k]`, where `0 <= k < i`; `dp[i]` means that considering only the first `i` characters and keeping the `i`-th column, the maximum number of retained columns.
2. Recursively define the value of the optimal solution: `dp[i] = max(dp[k] + 1), for every string .indexAt[i] >= .indexAt[k]`
3. Use the bottom-up method to solve the answer. For `i` from 0 to `len(A[0])`, compute `dp[i]`; the maximum retained columns is `max(dp)`. It is not `dp[-1]` because the last column does not necessarily have to be retained.
4. Since the problem does not require finding which columns are ultimately deleted or retained, constructing the optimal solution can be ignored.

Time complexity: O(A.length ^2 * A[i].length),
space complexity: O(A.length).

```python
class Solution:
    def minDeletionSize(self, A):
        """
        :type A: List[str]
        :rtype: int
        """
        dp = [1 for i in range(len(A[0]))]
        
        for i in range(len(dp)):
            for k in range(i):
                if all(s[k] <= s[i] for s in A):
                    dp[i] = max(dp[i], dp[k] + 1)
                
        return len(A[0]) - max(dp)
```

## Postscript

The revolution has not yet succeeded; comrades still need to work hard.

Recently I followed someone on Zhihu named Jennica, the person I asked for a Google referral. She loves life and programming, and I think I am actually like that too. So I plan to learn more from her. Maybe the course of history will somehow let me enter Google by accident. For a long time, at least over the past year, I have treated working at a foreign company as my career plan. Now that I see the effort and success of people ahead of me, I feel even more motivated and eager. I want to work as a programmer at Google or Microsoft, haha.
