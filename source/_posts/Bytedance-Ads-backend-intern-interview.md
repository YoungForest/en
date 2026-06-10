---
title: ByteDance Summer Internship Ads Backend Development Interview
date: 2020-02-27 18:34:40
tags:
- ByteDance
- Intern
categories:
- Interview
---

* HashMap
* Database
    * indexes, optimization, transactions
    * clustered index and non-clustered index
* Concurrent Programming
* Network Programming, RPC
* Algorithm problem:
    * Edit Distance


The algorithm question was to compute [Edit Distance (Levenshtein Distance)](https://en.wikipedia.org/wiki/Levenshtein_distance). I had happened to encounter edit distance before while reading *Grokking Algorithms*, and it can be solved with DP. But this problem was a bit more complex: among many strings, find the string with the smallest distance. It can be understood as "Fuzzy matching".
The rough statement was:
```
莱文斯坦距离，又称 Levenshtein 距离，是编辑距离的一种。指两个字串之间，由一个转成另一个所需的最少编辑操作次数。
允许的编辑操作包括：
插入一个字符
删除一个字符
将一个字符替换成另一个字符
需要你编写一个程序，实现以下功能：
给定一个字符串集合 S 以及一个模板串 P，从 S 中找出与 P 莱文斯坦距离最小的字符串 T，输出 T 以及其对应的编辑距离 D。如果 S 中出现多个满足条件的字符串，则取按字典序排列的第一个。
```

I did not think of a good solution. With brute force, comparing every string with `P` has time complexity: O(P.size() * sum(S_i.size())).

Later, I searched online for solutions, and they were not hard to find. Use a Trie to avoid repeated DP computation among different strings. The time complexity becomes O(P.size() * number of Trie nodes). Although the worst-case complexity does not improve, it is a real optimization. This should be the solution the interviewer wanted.

```cpp
#include 
#include 
#include 
#include 
#include 
#include 

using namespace std;

struct Node {
    array, 26> children;
    vector distance;
    Node() = delete;
    Node(int n) {
        distance.resize(n);
    }
};

pair solve(const string& target, const vector& s) {
    const int k = target.size() + 1;
    auto root = make_shared(k);
    for (int i = 0; i distance.size(); ++i) {
        root->distance[i] = i;
    }
    int ans_distance = 0x3f3f3f3f,  ans_index = -1;
    for (int j = 0; j < s.size(); ++j) {
        const string& str = s[j];
        auto current = root;
        // cout << endl << "debug: " << str << endl;
        int distance_from_empty = 0;
        for (char c : str) {
            if (current->children[c - 'a'] == nullptr) {
                current->children[c - 'a'] = make_shared(k);
                auto next_current = current->children[c - 'a'];
                next_current->distance[0] = distance_from_empty;
                for (int i = 1; i < k; ++i) {
                    if (c == target[i - 1]) {
                        next_current->distance[i] = current->distance[i - 1];
                    } else {
                        next_current->distance[i] = min({
                            current->distance[i - 1],
                            current->distance[i],
                            next_current->distance[i - 1]
                        }) + 1;
                    }
                    // cout distance[i] << " ";
                }
                // cout << endl;
            }
            current = current->children[c - 'a'];
            ++distance_from_empty;
        }
        if (current->distance[k - 1] < ans_distance) {
            ans_distance = current->distance[k - 1];
            ans_index = j;
        } else  if (current->distance[k - 1] == ans_distance) {
            if (s[ans_index] > s[j])
                ans_index = j;
        }
    }
    return {ans_distance, s[ans_index]};
}

int main() {
    string P;
    cin >> P;
    int N;
    cin >> N;
    vector S(N);
    for (int i = 0; i < N; ++i) {
        cin >> S[i];
    }
    auto ans = solve(P, S);
    cout << ans.first << endl;
    cout << ans.second << endl;
    return 0;
}
```
