---
title: LeetCode weekly contest 156
date: 2019-10-04 12:41:57
description: Post-contest notes on unique occurrence counts, equal substrings within budget, adjacent-duplicate removal and a rotating snake shortest path.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/10/04/LeetCode-weekly-contest-156/
  en: https://youngforest.github.io/en/2019/10/04/LeetCode-weekly-contest-156/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-156/en-hero.webp" alt="A wordless workshop links distinct bead towers, a budgeted sliding frame, collapsing colour groups and a rotating two-link wooden snake" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

Solved the problems after the contest.

## 1207. Unique Number of Occurrences

Record the number of occurrences of each value by `unordered_map`.
Check the unique using `unordered_set`.

Time complexity: O(N),
Space complexity: O(N).

```cpp
class Solution {
public:
    bool uniqueOccurrences(vector<int>& arr) {
        unordered_set<int> count;
        unordered_map<int, int> seen;
        for (int i : arr) {
            ++seen[i];
        }
        for (const auto& p : seen) {
            if (count.find(p.second) != count.end()) {
                return false;
            } else {
                count.insert(p.second);
            }
        }
        return true;
    }
};
```

## 1208. Get Equal Substrings Within Budget

Sliding window.
Use a sliding window to represent the available substring.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
public:
    int equalSubstring(string s, string t, const int maxCost) {
        int left = 0, right = 0;
        // window: [left, right), length = right - left
        int cost = 0;
        int ans = 0;
        while (left < s.size()) {
            while (right < s.size() && cost + std::abs(s[right] - t[right]) <= maxCost) {
                cost += std::abs(s[right] - t[right]);
                ++right;
                ans = max(ans, right - left);
            }
            cost -= std::abs(s[left] - t[left]);
            ++left;
        }
        return ans;
    }
};
```

## 1209. Remove All Adjacent Duplicates in String II

One pass scan  and remove duplicates.

```cpp
class Solution {
public:
    string removeDuplicates(string s, int k) {
        string ans;
        vector<int> accu; // accumulate
        for (char c : s) {
            int new_accu;
            if (!ans.empty() && c == ans.back()) {
                new_accu = accu.back() + 1;
            } else {
                new_accu = 1;
            }
            if (new_accu >= k) {
                for (int i = 0; i < new_accu - 1; ++i) {
                    ans.pop_back();
                    accu.pop_back();
                }
            } else {
                ans.push_back(c);
                accu.push_back(new_accu);
            }
        }
        return ans;
    }
};
```

## 1210. Minimum Moves to Reach Target with Rotations

It asks for the minimum number of steps, and n is at most 100, so BFS can solve it.
The idea of this problem is not hard. The hard part is implementation. Since it is not a direct BFS, there are many details to pay attention to during implementation.

Time complexity: O(N ^ 2),
space complexity: O(N ^ 2).

Because I recently tried learning the emerging language Rust, some of the following problems will use Rust instead of C++. The purpose is to give myself a chance to practice and become familiar with this new language. Students who have time and interest can also learn this new language. If your previous main language was C++, learning RUST is really satisfying.

```rust
use std::collections::HashSet;
use std::collections::VecDeque;

impl Solution {
    pub fn minimum_moves(grid: Vec<Vec<i32>>) -> i32 {
        // bfs
        let n = grid.len() as i32;
        assert_eq!(n >= 2, true);
        let m = grid[0].len() as i32;
        assert_eq!(n, m);
        let mut q = VecDeque::new();
        let mut seen = HashSet::new();
        let mut level = 1;
        q.push_back(((0, 0), (0, 1)));
        seen.insert(((0, 0), (0, 1)));
        while !q.is_empty() {
            let size = q.len();
            for _ in 0..size {
                let current = q.pop_front();
                match current {
                    Some(value) => {
                        let next_step: [((i32, i32), (i32, i32)); 4] = [
                            ((0, 1), (0, 1)),
                            ((1, 0), (1, 0)),
                            ((0, 0), (1, -1)),
                            ((0, 0), (-1, 1)),
                        ];
                        for i in 0..4 {
                            let m = next_step[i];
                            let new_tail = (value.0 .0 + m.0 .0, value.0 .1 + m.0 .1);
                            let new_head = (value.1 .0 + m.1 .0, value.1 .1 + m.1 .1);
                            if new_tail.0 < n
                                && new_tail.1 < n
                                && new_head.0 < n
                                && new_head.1 < n
                                && new_tail.0 >= 0
                                && new_tail.1 >= 0
                                && new_head.0 >= 0
                                && new_head.1 >= 0
                                && grid[new_tail.0 as usize][new_tail.1 as usize] == 0
                                && grid[new_head.0 as usize][new_head.1 as usize] == 0
                                && !seen.contains(&(new_tail, new_head))
                            {
                                if i == 0
                                    || i == 1
                                    || (value.0 .0 + 1 >= 0 // old_tail + 1 is in range
                                        && value.0 .0 + 1 < n
                                        && value.0 .1 + 1 >= 0
                                        && value.0 .1 + 1 < n
                                        && grid[(value.0 .0 + 1) as usize]  // old_tail + 1 is unblock
                                            [(value.0 .1 + 1) as usize]
                                            == 0
                                        && ((i == 2 && value.0 .0 == value.1 .0)
                                            || (i == 3 && value.0 .1 == value.1 .1)))
                                {
                                    if new_tail == (n - 1, n - 2) && new_head == (n - 1, n - 1) {
                                        return level;
                                    }
                                    q.push_back((new_tail, new_head));
                                    seen.insert((new_tail, new_head));
                                }
                            }
                        }
                    }
                    None => (),
                };
            }
            level += 1;
        }
        return -1;
    }
}
```
