---
title: LCCUP 21 Fall Team
date: 2021-09-25 20:28:48
tags:
- Competitive Programming
categories:
- LeetCode
---

| Rank |	Name |	Score |	Finish Time | 	Q1 (2) |	Q2 (4) |	Q3 (6) |	Q4 (7)| Q5 (8) | Q6(9) |
|--|--|--|--|--|--|--|--|--|--|
| 81 / 1363 | 爸爸去哪儿 | 19/37 | 	1:07:44 | 0:03:40 by 爸 | 0:21:39 by 爸 | 0:53:13 by 宝  | 1:02:44 🐞 1 by 爸 | null | null |

For this year's fall contest, I only participated in the team contest, not the individual contest. In the spring contest, I teamed up with Uncle Lai and Brother Xianmu, and the result was not very ideal. In the fall contest, I teamed up with Baobao as a two-person team, and the result was actually quite good. In particular, Baobao not only did not slow me down, but also made an important contribution to the team. Without her smoothly solving Q3, our team's rank would definitely not look as good as it does now.

After solving four problems in one hour, only five people AC'd the last two problems. After staring at them for a long time, we did not find any idea with a feasible time complexity. So we decisively gave up, and the last two hours were saved for doing something else.

Although I now use my [English blog](https://youngforest.github.io/en/) to write weekly contest summaries, both the spring and fall contests are events on the China LeetCode site, and only Chinese users can participate in and see them. So considering the audience, this contest summary was still written in Chinese.

## [LCP 44. Opening Ceremony Fireworks](https://leetcode-cn.com/problems/sZ59z6/)

A warm-up problem. DFS, using a set to record different colors.

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
    int numColor(TreeNode* root) {
        set<int> colors;
        function<void(TreeNode*)> dfs = [&](TreeNode* root) -> void {
            if (root) {
                colors.insert(root->val);
                dfs(root->left);
                dfs(root->right);
            }
        };
        dfs(root);
        return colors.size();
    }
};
```

Time complexity: O(N),
space complexity: O(N).
Here, `N` is the number of nodes.

## [LCP 45. Bicycle Yard](https://leetcode-cn.com/problems/kplEvH/)

DFS. Because height needs to be considered, pass height in as a parameter as well.

Use `seen`/`visit` to record visited states.

Because of the speed-change mechanism `h1-h2-o2`, the speed obviously cannot exceed `maximum height + 1`, so the number of states is also finite, roughly `n\*m\*maximum height = 10^6`.

```python
class Solution:
    def bicycleYard(self, position: List[int], terrain: List[List[int]], obstacle: List[List[int]]) -> List[List[int]]:
        n = len(terrain)
        m = len(terrain[0])
        minSpeed = [[float('inf')] * m for _ in range(n)]
        direction = ((1, 0), (0, 1), (0, -1), (-1, 0))
        seen = set()
        def dfs(i, j, speed):
            if speed <= 0: return
            if (i, j, speed) in seen: return
            seen.add((i, j, speed))
            minSpeed[i][j] = min(minSpeed[i][j], speed)
            for di, dj in direction:
                ni = i + di
                nj = j + dj
                if ni < n and ni >= 0 and nj >= 0 and nj < m:
                    newSpeed = speed + terrain[i][j] - terrain[ni][nj] - obstacle[ni][nj]
                    if (ni, nj, newSpeed) not in seen:
                        dfs(ni, nj, newSpeed)
        
        dfs(position[0], position[1], 1)
        minSpeed[position[0]][position[1]] = float('inf')
        ans = []
        for i in range(n):
            for j in range(m):
                if minSpeed[i][j] == 1:
                    ans.append([i, j])
        return ans
```

Time complexity: O(m * n * maxTerrain),
space complexity: O(m * n * maxTerrain).

## LCP 46. Volunteer Deployment

This solution was independently figured out and implemented by Baobao. Before implementation, we did discuss the approach and confirmed that it was correct before she started coding.

Set up equations. For each venue on each day, represent the headcount as `(coefficient, constant)`. For example, on the final day, venue 0 is `(1, 0)`, while the others are `(0, number of people)`. Then derive backward and update the venue counts. When reaching the first day, solve the equation.

```python
class Solution:
    def volunteerDeployment(self, finalCnt: List[int], totalNum: int, edges: List[List[int]], plans: List[List[int]]) -> List[int]:
        neighbour = collections.defaultdict(list)
        for a,b in edges:
            neighbour[a].append(b)
            neighbour[b].append(a)
        coef = [[1,0]]
        for c in finalCnt:
            coef.append([0,c])
        for i in range(len(plans)-1, -1, -1):
            num, idx = plans[i]
            if num == 1:
                coef[idx] = [coef[idx][0]*2, coef[idx][1]*2]
            elif num == 2:
                for nei in neighbour[idx]:
                    coef[nei] = [coef[nei][0]-coef[idx][0], coef[nei][1]-coef[idx][1]]
            else:
                for nei in neighbour[idx]:
                    coef[nei] = [coef[nei][0]+coef[idx][0], coef[nei][1]+coef[idx][1]]
        divide = 0
        for a,b in coef:
            totalNum -= b
            divide += a
        x = totalNum//divide
        ans = []
        for a,b in coef:
            ans.append(a*x+b)
        return ans
```

Time complexity: O(m * n * avg(edges)),
space complexity: O(edges + n).

## LCP 47. Security Check

When I saw that the final answer could be very large and needed modulo, it was basically impossible to enumerate all cases. It was most likely a `DP` problem.

The next question was what the state transition should look like.

This problem is quite similar to the Josephus problem. The focus is the change in numeric indices when using stack/queue behavior.

Because it involves top-down DP and large-number modulo arithmetic, `Python` has an obvious advantage.

Although `@cache` is convenient, I often forget `dp.cache_clear()`, which caused one TLE penalty.

I need to pay attention to this in the future.

```python
class Solution:
    def securityCheck(self, capacities: List[int], k: int) -> int:
        MOD = 1000000007
        n = len(capacities)
        @cache
        def dp(i, k):
            # the current first capcities index, k is the number we want to pop first
            # print(i,k)
            if i == n - 1:
                ans = 0
                if k == 0: # queue
                    ans += 1
                if k == capacities[n-1] - 1:
                    ans += 1
                return ans
            else:
                ans = 0
                c = capacities[i]
                if k >= c - 1:
                    ans += dp(i+1, k - (c - 1)) # stack
                
                ans += dp(i+1, k) # queue
                return (ans) % MOD
        
        ans = dp(0, k)
        dp.cache_clear()
        return ans
```

Time complexity: O(n * k),
space complexity: O(n * k).

## LCP 48. Infinite Chessboard

## LCP 49. Ring Adventure Game
