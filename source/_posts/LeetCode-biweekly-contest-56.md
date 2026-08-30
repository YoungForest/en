---
title: LeetCode biweekly contest 56
date: 2021-07-12 18:20:27
description: "Four solutions enumerate square-sum triples, use breadth-first search for the nearest exit, play a two-half sum game, and minimize cost under a time limit."
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2021/07/12/LeetCode-biweekly-contest-56/
  en: https://youngforest.github.io/en/2021/07/12/LeetCode-biweekly-contest-56/
---
| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (5) |	Q4 (6)|
|--|--|--|--|--|--|--|--|
| 998 / 10896 | YoungForest | 18 | 	1:27:14 | 0:02:33 | 0:13:25 |  1:17:14  🐞2 | null |

The number of participants in biweekly contests has almost caught up with weekly contests.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-biweekly-contest-56/en-hero.webp" alt="Square tiles form a right triangle, maze waves reach the nearest exit, blank counters balance two halves and timed routes compare costs" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

## 1925. Count Square Sum Triples

A warm-up problem. Brute-force enumerate all combinations.

```python
class Solution:
    def countTriples(self, n: int) -> int:
        ans = 0
        for c in range(1, n+1):
            for b in range(1, c+1):
                for a in range(1, b+1):
                    if a * a + b * b == c * c:
                        if a == b: ans += 1
                        else: ans += 2
        return ans
```

Time complexity: O(N^3),
Space complexity: O(1).

## 1926. Nearest Exit from Entrance in Maze

Find the nearest exit. Standard BFS.

```python
class Solution:
    def nearestExit(self, maze: List[List[str]], entrance: List[int]) -> int:
        visited = set()
        q = deque()
        start = (entrance[0], entrance[1])
        visited.add(start)
        q.append(start)
        step = 0
        directions = [(1, 0), (0, 1), (-1, 0), (0, -1)]
        rows = len(maze)
        cols = len(maze[0])
        def exitCell(cell):
            return (cell[0] == 0 or cell[0] == rows - 1 or cell[1] == 0 or cell[1] == cols - 1) and maze[cell[0]][cell[1]] == '.' and cell != start 
        while q:
            s = len(q)
            for _ in range(s):
                current = q.popleft()
                if exitCell(current): return step
                for di, dj in directions:
                    ni = di + current[0]
                    nj = dj + current[1]
                    if ni >= 0 and ni < rows and nj >= 0 and nj < cols and maze[ni][nj] == '.' and (ni, nj) not in visited:
                        visited.add((ni, nj))
                        q.append((ni,nj))
            step += 1
        
        return -1
```

Time complexity: O(m * n),
Space complexity: O(m * n).

## 1927. Sum Game

Greedy.

Try to pick from the side with fewer question marks first until that side is exhausted. Alice tries to make the difference larger, and Bob tries to make the difference smaller.

The idea was fine, but the code I wrote in the end was ugly and long. I only got it accepted 13 minutes before the contest ended.

```python
class Solution:
    def sumGame(self, num: str) -> bool:
        n = len(num)
        leftSum = 0
        rightSum = 0
        left = 0
        right = 0
        for i in range(n):
            if num[i] == '?':
                if i >= n // 2: right += 1
                else: left += 1
            else:
                if i >= n // 2: rightSum += ord(num[i]) - ord('0')
                else: leftSum += ord(num[i]) - ord('0')
        total = right + left
        # print ('total: ', total)
        if total % 2 == 1: return True
        alice = True
        for _ in range(total):
            if alice:
                if left == 0 or (right != 0 and left > right): # pick right
                    if leftSum <= rightSum or rightSum + 9 > leftSum:
                        rightSum += 9
                    else:
                        rightSum += 0
                    right -= 1
                elif right == 0 or left < right: # pick left
                    if leftSum >= rightSum or leftSum + 9 > rightSum:
                        leftSum += 9
                    else:
                        leftSum += 0
                    left -= 1
                else:
                    if leftSum == rightSum:
                        return False
                    elif leftSum > rightSum:
                        leftSum += 9
                        left -= 1
                    else:
                        rightSum += 9
                        right -= 1
                        
            else:
                if left == 0 or (right != 0 and left > right): # pick right
                    if leftSum > rightSum:
                        rightSum +=  9 if (leftSum - rightSum > 9) else leftSum - rightSum
                    else:
                        rightSum += 0
                    right -= 1
                elif right == 0 or left < right: # pick left
                    if leftSum < rightSum:
                        leftSum += 9 if (rightSum - leftSum > 9) else rightSum - leftSum
                    else:
                        leftSum += 0
                    left -= 1
                    
            # print (left, leftSum, rightSum, right)
                
            alice = not alice
            
        return leftSum != rightSum
```

Time complexity: O(num.length),
Space complexity: O(num.length).

## 1928. Minimum Cost to Reach Destination in Time

This is also a classic problem: minimize the cost under a time constraint.

[Official CN DP solution](https://leetcode-cn.com/problems/minimum-cost-to-reach-destination-in-time/solution/gui-ding-shi-jian-nei-dao-da-zhong-dian-n3ews/)
