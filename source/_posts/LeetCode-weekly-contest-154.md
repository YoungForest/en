---
title: LeetCode weekly contest 154
date: 2019-09-18 10:08:36
description: Post-contest notes on frequency construction, nested reversal, repeated-array maximum sums and critical network connections across time zones.
tags:
- Competitive Programming
categories:
- LeetCode
translations:
  zh-CN: https://youngforest.github.io/2019/09/18/LeetCode-weekly-contest-154/
  en: https://youngforest.github.io/en/2019/09/18/LeetCode-weekly-contest-154/
---
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/LeetCode-weekly-contest-154/en-hero.webp" alt="Forest watches dawn beside paired balloon beads, reversing nested loops, a repeated belt and a network crossed by one fragile bridge" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

Last weekend I was in Belgium, and the contest time was from 4:30 a.m. to 6:00 a.m. The timing was unsuitable, so I did not participate. I found that only the biweekly contest time, Saturday from 4:30 p.m. to 6:00 p.m., is somewhat suitable. My goal of ranking 2000 this year is probably going to be postponed. Even in the best case, the number of contests I can participate in is only one-third of what it would be in China.

## 1189. Maximum Number of Balloons

Just count the frequency of each letter. Note that `l` and `o` each need to appear twice to form one `balloon`.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
public:
    int maxNumberOfBalloons(string text) {
        const string once = "ban";
        const string twice = "lo";
        vector<int> count(26, 0);
        for (char c : text) {
            ++count[c - 'a'];
        }
        int ans = numeric_limits<int>::max();
        for (char c : once) {
            ans = min(ans, count[c - 'a']);
        }
        for (char c : twice) {
            ans = min(ans, count[c - 'a'] / 2);
        }
        return ans;
    }
};
```

## 1190. Reverse Substrings Between Each Pair of Parentheses

Because the maximum string length is 2000, a brute-force method is enough. Use a stack for parenthesis matching.

Time complexity: O(N ^ 2),
space complexity: O(N).

```cpp
class Solution {
public:
    string reverseParentheses(string s) {
        stack<int> st;
        for (int i = 0; i < s.size(); ++i) {
            auto& c = s[i];
            if (c == ')') {
                int begin = st.top();
                st.pop();
                reverse(s.begin() + begin, s.begin() + i);
            } else if (c == '(') {
                st.push(i);
            }
        }
        string ans;
        for (char c : s) {
            if (c != ')' && c != '(') {
                ans.push_back(c);
            }
        }
        return ans;
    }
};
```

After reading the discuss section, I found a solution with time complexity O(N).
The first pass finds all matching parentheses, and the second pass constructs the result string. Each parenthesis acts like a portal: it changes the direction of movement while jumping to the matching parenthesis.

```cpp
string reverseParentheses(string s) {
    int n = s.length();
    vector<int> opened, pair(n);
    for (int i = 0; i < n; ++i) {
        if (s[i] == '(')
            opened.push_back(i);
        if (s[i] == ')') {
            int j = opened.back();
            opened.pop_back();
            pair[i] = j;
            pair[j] = i;
        }
    }
    string res;
    for (int i = 0, d = 1; i < n; i += d) {
        if (s[i] == '(' || s[i] == ')')
            i = pair[i], d = -d;
        else
            res += s[i];
    }
    return res;
}
```

## 1191. K-Concatenation Maximum Sum

This is the maximum subarray sum problem. The special point is that the array can be repeated k times.
By observing carefully, we can see that when the sum of the entire array is less than or equal to 0, according to a greedy idea, the final answer must be the answer formed by repeating the array twice: either a subarray within the array itself, or the maximum prefix sum plus the maximum suffix sum.
When the sum of the entire array is greater than 0, the answer is the sum of k - 2 arrays plus the maximum prefix sum plus the maximum suffix sum.

Time complexity: O(N),
space complexity: O(1).

```cpp
class Solution {
    using ll = long long;
    const ll mod = 1e9 + 7;
public:
    int kConcatenationMaxSum(vector<int>& arr, int k) {
        ll head = 0, tail = 0;
        ll global_max = 0, local_max = 0;
        ll prefix = 0, total_sum = 0;
        for (ll i = 0; i < arr.size(); ++i) {
            if (arr[i] + prefix < 0) {
                prefix = 0;
            } else {
                prefix = arr[i] + prefix;
                local_max = max(local_max, prefix);
            }
            total_sum += arr[i];
            head = max(head, total_sum);
            tail = min(tail, total_sum);
        }
        tail = total_sum - tail;
        tail = prefix;
        if (total_sum > 0) {
            global_max = tail + head + total_sum * (k - 2);
        } else {
            global_max = head + tail;
        }
        
        global_max = max(global_max, local_max);
        return global_max % mod;
    }
};
```

## 1192. Critical Connections in a Network

