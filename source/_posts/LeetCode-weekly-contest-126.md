---
title: LeetCode weekly contest 126
date: 2019-03-03 11:55:17
tags:
- Competitive Programming
categories:
- LeetCode
---

Today I tried recording video while solving problems. Due to venue limitations, I could not use a microphone to explain, so the result was barely satisfactory. Although I could compensate a bit with text annotations, the biggest advantage of video communication was lost. In the future, I should still focus on blogging to spread my thoughts.
Especially since this time I only solved two problems. I attempted both of the last two problems, but failed on both. The video effect was too poor. If people watch videos on Bilibili, they are there to see the uploader show off. This time I did not manage to show off and instead met my Waterloo, which was quite embarrassing. But in the end I still plan to upload the video. I am just that thick-skinned: not afraid of embarrassment, and not afraid that future people will dig up my old shame.

My rank also directly flew beyond 1500. My ranking this week is probably going to drop.

Result:

| Rank |	Name |	Score |	Finish Time | 	Q1 (3) |	Q2 (5) |	Q3 (6) |	Q4 (9)|
|--|--|--|--|--|--|--|--|
| 1684 / 4564	| YoungForest | 	8 |	0:38:19 |	0:16:17 |	0:33:19  WA(1) | |	 |

## 1002. Find Common Characters

Because I wrote the idea at the beginning while recording the video, I will only analyze the complexity here.
Time complexity: O(N), where N is the total length of all strings in A.
Space complexity: O(1), because characters are limited to 26 lowercase letters. Otherwise, it would be proportional to the number of character types.

```cpp
class Solution {
public:
    vector<string> commonChars(vector<string>& A) {
        // 用一个vector记录出现的字母，因为只有小写，大小为26，还因为要记录出现的最小次数，所以类型为int即可
        vector<int> record(26, numeric_limits<int>::max());
        for (string & s: A) {
            vector<int> record1String(26, 0);
            for (char c : s) {
                record1String[c - 'a']++;
            }
            for (int i = 0; i < 26; ++i) {
                record[i] = min(record[i], record1String[i]);
            }
        }
        
        vector<string> result;
        for (int i = 0; i < 26; i++) {
            result.insert(result.end(), record[i], string(1, char(i + 'a')));
        }
        
        return result;
    }
};
```

## 1003. Check If Word Is Valid After Substitutions

Time complexity: O(N ^ 2), because `find` and constructing `nextS` are O(N). Each `while` loop reduces the length of S by 3, so it needs length / 3 iterations.
Space complexity: O(N), because a new `nextS` is constructed. This can be eliminated by repeatedly reusing the original space of S, but that would require extra code.

```cpp
class Solution {
public:
    bool isValid(string S) {
        // 首先理解valid string的定义:
        // 这个定义的递归的
        // 对于任何valid的string，把"abc"插入任何位置的字符串仍为valid
        // 初始的valid string只有"abc"
        // 所以，判断一个string是否是valid的，我们只需要不断地把"abd"抽掉，如果剩下"abc"，则true，否则false
        // 这个过程也可以递归进行
        
        // 递归解法会stack overflow, 换迭代解法
        while (true) {
            if (S == "abc") return true;
            auto index = S.find("abc");
            if (index == string::npos) { // 没找到"abc"字串
                return false;
            } else {
                string nextS(S.begin(), S.begin() + index);
                nextS.insert(nextS.end(), S.begin() + index + 3, S.end());
                S = nextS;
            }
        }
    }
};
```

## 1004. Max Consecutive Ones III

After reading the discussion section, I was startled to realize that this should use sliding window. Actually, if one is familiar with this type of problem, it is still a very intuitive idea. This problem also pointed straight at a blind spot in my knowledge. To be honest, I am not familiar enough with sliding window, or two pointers, problems. When I saw this problem, I kept trying to use DP and greedy. I spent a lot of time and still failed to solve it, which is not surprising.

```cpp
class Solution {
public:
    int longestOnes(vector<int>& A, int K) {
        // 知道用sliding window了，题目就很简单了。用指针left, right表示窗口的范围。如果right指向1的话，继续扩充，指向0的话，如果K还有剩余，则扩充。不能扩充的时候，收缩left。
        int left = 0, right = 0;
        int remain = K;
        int result = 0;
        while (right < A.size()) {
            if (A[right] == 1) right++;
            else if (A[right] == 0 && remain > 0) {
                right++;
                remain--;
            } else if (A[left] == 0) {
                left++;
                remain++;
            } else if (A[left] == 1) {
                left++;
            } else {
                assert(false);
            }
            result = max(result, right - left);
        }
        
        return result;
    }
};
```

Time complexity: O(n). This is also the characteristic of sliding window: the left and right pointers each only need to move once.
Space complexity: O(1).

Also using sliding window, the code from discussion master lee215 is like this:
```cpp
    int longestOnes(vector<int>& A, int K) {
        int i = 0, j;
        for (j = 0; j < A.size(); ++j) {
            if (A[j] == 0) K--;
            if (K < 0 && A[i++] == 0) K++;
        }
        return j - i;
    }
```

Because we want to find the largest window, shrinking the window is unnecessary. That is why the master's code is so concise.

## 1000. Minimum Cost to Merge Stones

LeetCode's 1000th problem is indeed very hard. I read other people's Discuss posts for about an hour before understanding it.
To be honest, DP is also a blind spot in my knowledge. When I am lucky I can solve it; when I am not, I cannot. As Huahua said, DP is the type where even after solving 100 problems, you may still fail to solve a new one.
Because the Discuss posts were too hard to understand, after I understood it myself, I wanted to write the blog post in as much detail as possible.

There are two key points in a DP problem:
1. Optimal substructure
2. Overlapping subproblems

Let `dp[i][j]` represent the minimum cost required to merge `stone[i] ~ stone[j]`.
Here, "merge" means merging these piles of stones until fewer than K piles remain. At that point, even if you want to continue merging, you cannot. What can be determined is that once `i` and `j` are fixed, the number of piles remaining after merging is fixed, namely `(j - i + 1) % (k - 1)`.

At this point, the optimal substructure is $dp[i][j] = min(dp[i][mid] + dp[mid+1][j]) + (\sum_k^{i<=k<=j} stone[i] if (j - i) % (k - 1) == 0), for mid in range(i, j, k-1)$.
`(j - i) % (k - 1) == 0` means the piles in `[i, j]` can be merged into 1, so a merge operation will definitely be performed, and the cost is the total number of stones. Why is the step for `mid` `k - 1`? Because only this way does `dp[i][mid]` leave 1 pile remaining.

After finding the optimal substructure, the next step is to determine how to compute `dp[0][n-1]` bottom-up.
Initialization: `dp[i][i] = 0`. One pile of stones never needs to be merged.
According to the optimal substructure, we can obtain the computation order shown below:
![](/images/leetcode1000 merge stone dp bottom to top computing order.png)

In the figure, the red line indicates the outermost loop, the green line indicates the inner loop, and the yellow line indicates the optimal substructure.

```cpp
class Solution {
public:
    int mergeStones(vector<int>& stones, int K) {
        int N = stones.size();
        if ((N - 1) % (K - 1) != 0) return -1;
        vector<int> prefix(N);
        prefix[0] = stones[0];
        for (int i = 1; i != N; ++i) {
            prefix[i] = prefix[i-1] + stones[i];
        }

        vector<vector<int>> dp(N, vector<int>(N, 0));
        for (int m = K; m <= N; m++) {
            for (int i = 0; i + m <= N; i++) {
                int j = i + m - 1;
                dp[i][j] = numeric_limits<int>::max();
                for (int mid = i; mid < j; mid += K - 1) {
                    dp[i][j] = min(dp[i][j], dp[i][mid] + dp[mid+1][j]);
                }
                if ((j - i) % (K - 1) == 0)
                    dp[i][j] += prefix[j] - (i == 0 ? 0 : prefix[i-1]);
            }
        }
        
        return dp[0][N-1];
    }
};
```

Time complexity: O(N^3 / K),
Space complexity: O(N^2).

Similar problems include: [312. Burst Balloons](https://leetcode.com/problems/burst-balloons/description/)
