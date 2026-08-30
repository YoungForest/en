---
title: LeetCode 134 Gas Station
date: 2019-09-06 16:56:01
description: Revisiting a failed interview problem six months later, deriving a linear circular-route solution that skips every doomed starting point.
tags:
- Algorithm
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/09/06/LeetCode-134-Gas-Station/
  en: https://youngforest.github.io/en/2019/09/06/LeetCode-134-Gas-Station/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-134-Gas-Station/en-hero.webp" alt="Forest reroutes a circular energy course past a failed segment, leaving tangled trial paths behind for one clear loop ahead" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

[Problem description](https://leetcode.com/problems/gas-station/description/)

This problem was one I encountered in my Google intern phone interview in February. At that time I solved it in a very messy way and failed the first round directly. Today, after seeing [a classmate's explanation](https://zhuanlan.zhihu.com/p/81412559), I decided to try this problem again. After all, in the past half year I have solved 500+ problems, and my algorithm ability has grown somewhat. I only saw the problem title in the explanation, not the content, so I guess I was able to solve this problem independently half a year later. After AC, I actually cried, sad about how insufficient my ability was back then. I do not know whether I will have such a good opportunity again.
In these six months I also participated in Kick Start three times. Except for receiving a resume notification in Round A the first time, I failed in both Round D and Round E.
As I have said repeatedly before, I really want to go to foreign companies like Google and Microsoft, and I have also put half a year of effort into it. I hope hard work will pay off. If I can get a Google or Microsoft summer internship next year, I will reward myself with a self-guided trip to South Korea during the Dragon Boat Festival holiday. A childhood friend is there, so I can go visit her.

The idea of this problem is as follows:

First, the brute-force O(N^2) solution is simple.
Enumerate all starting points and simulate the circular process from that start.
Two prefix sum arrays, `gas_prefix` and `cost_prefix`, can be used to track fuel and cost, ensuring fuel is always greater than or equal to cost.

Then try to optimize further by thinking about whether information between different starting points can be reused.
Observation: if at the i-th gas station the fuel is less than the cost, then all gas stations from the start to i inclusive do not need to be tried as starting points. Because if `start < j <= i`, then when starting from `j` and arriving at `i`, `gas_prefix = old start's gas_prefix - old start to j's gas_prefix`, and similarly `cost_prefix = old start's cost_prefix - old start to j's cost_prefix`. Since old start to j is successful, we have `old start to j's gas_prefix >= old start to j's cost_prefix`, so `gas_prefix` is still less than `cost_prefix`. `j` does not need to be tried and can be skipped directly.
So we have an O(N) solution, where the start jumps directly to the node after the failed node.

My classmate's solution proves the correctness of this optimization by contradiction, which is also a very good method.

```cpp
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        const int n = gas.size();
        vector<int> gas_prefix(n);
        vector<int> cost_prefix(n);
        for (int begin_index = 0; begin_index < n; ) {
            int i = 0;
            for (; i < n; ++i) {
                if (i == 0) {
                    gas_prefix[i] = gas[(begin_index + i) % n];
                    cost_prefix[i] = cost[(begin_index + i) % n];
                } else {
                    gas_prefix[i] = gas_prefix[i - 1] + gas[(begin_index + i) % n];
                    cost_prefix[i] = cost_prefix[i - 1] + cost[(begin_index + i) % n];
                }
                if (gas_prefix[i] < cost_prefix[i]) {
                    begin_index += i + 1;
                    break;
                }
            }
            if (i == n) {
                return begin_index;
            }
        }
        return -1;
    }
};
```
