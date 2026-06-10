---
title: Kick Start 2019 Round H
date: 2019-11-25 22:11:22
tags:
- Competitive Programming
categories:
- KickStart
---

Because Europe was on winter time, I was in UTC +1.00, so for this contest I deliberately got up at 6 a.m. to participate. After finishing, I rested for an hour, then went to Ghent with friends for the whole day. When I came back at night, I suddenly remembered that Machine Learning assignment 2 was due that day, and then started rushing the deadline like crazy. Facts proved that without studying seriously, I still could not handle the assignment. I submitted something rough; better than not submitting at all. For the blank questions, I generously told the TA that I simply did not know how to do them.

This was the last round of Kick Start 2019, and I still really wanted to participate. This year I participated in six Kick Start rounds in total. Although I already got an interview invitation in Round A, I still could not afford to be careless. The rankings for each round are shown below:

| Round | A | D | E | F | G | H |
|-----|---|---|---|---|---|---|
|Rank |600| 765 | 1566 | 1341 | 462 | 330 |
| Score|35| 27 | 22 | 11 | 42 | 41 |

Except for Round F, when I was traveling in Paris and just messed around with the problems at night in the hostel, I can say I gave my full effort in the other rounds.
Overall, my rank first went down and then went up. It does not reflect a change in ability so much as a change in mindset. Round D was relatively important, and at that time a classmate was competing too, so I did not have the heart to communicate with him during the contest. During Round E, I was at ByteDance's summer camp and solved it together with a girl. I did want to do well, but because my Union-Find did not implement path compression in `find`, it caused TLE. This was also a blind spot I had not noticed before. The other three rounds with relatively good results were instead the ones where I had no burden and was more relaxed.

Recently I also unexpectedly received an invitation to Google's Beijing A Day with Google. Unfortunately I was not in Beijing anymore, so I had to decline. In addition, I emailed Google to report that all Google recruiting-related forms did not include Beihang University in the "My University" options. I guessed this might be because Beihang was on the U.S. Department of Commerce blacklist. Google resolved it quickly. From now on, my Beihang is no longer treated like a diploma mill.

In this contest, I solved the warm-up problem and the small test set 1 of problem 3. I guess that counts as a normal performance.

## A. H-index

The first time I tried it, I misunderstood the statement and thought it only asked for the final H-index. I simply wrote a binary search solution that used a decision problem to find the maximum value.
Later I realized that the result after each paper was needed, and then several bugs I wrote affected the time cost.

Intuition:
As papers are published, the H-index is monotonically non-decreasing. Using this property, after each paper is published, try to increase the H-index.
Here I used the ordered property of C++ STL `set` and the iterator stability of node-based containers after inserting new nodes, and efficiently implemented the operation of trying to increase the H-index.

Time complexity: O(N log N),
space complexity: O(N).

```cpp
#include <algorithm>
#include <iostream>
#include <set>
#include <vector>

using namespace std;

int MAX_N = 1e5 + 5;

void solve(const vector<int> &A, int N) {
    multiset<int, std::greater<int>> s;
    int ans = 0;
    auto it = s.begin();
    for (int i = 0; i < N; ++i) {
        int come = A[i];
        if (come > 0) {
            s.insert(come);
            if (s.size() == 1) {
                it = s.begin();
                if (come >= 1) {
                    ans = 1;
                }
            }
            else if (come > *it) {
                --it;
                // cout << " --it " << *it << " ";
                // if (next(it) != s.end()) {
                //     cout << " next(it): " << *(next(it)) << " ";
                // }
            }
            if (it != s.end() && next(it) != s.end() && *next(it) >= ans + 1) {
                ++ans;
                ++it;
            }
        }
        cout << ans << " ";
    }
}

int main() {
    int T;
    cin >> T;
    vector<int> A(MAX_N);
    for (int iCase = 0; iCase < T; ++iCase) {
        int N;
        cin >> N;
        for (int i = 0; i < N; ++i) {
            cin >> A[i];
        }
        cout << "Case #" << iCase + 1 << ": ";
        solve(A, N);
        cout << endl;
    }
}
```

## B. Diagonal Puzzle

During the contest I tried to enumerate all flips through backtracking to find the minimum solution, with time complexity O(2 ^ (4 * N)). I did not expect even the smallest dataset to time out. So the solutions below were learned from the official Analysis after the contest.

### Small Test Set

There is no need to enumerate all flips. We can enumerate only the flips in one direction, then check whether every line in the other direction has the same color.
There are `2 * N - 1` flips parallel to the main diagonal, and also `2 * N - 1` flips perpendicular to the main diagonal.
The complexity of checking same color is `O(N ^ 2)`.
So the total time complexity is `O(2 ^ (2 * N - 1) * N ^ 2)`.

### Large Test Set

This solution is based on an interesting observation: if we determine the flips of the main diagonal and anti-diagonal, then in order to make everything white in the end, all other flips can be determined from that. When N is odd, it is the main diagonal and the secondary anti-diagonal. The cases for `N == 6, 7` are shown below.

```
\..../ \......
.\../. .\..../
..\/.. ..\../.
../\.. ...\/..
./..\. .../\..
/....\ ../..\.
       ./....\
```

So we can enumerate the flip states of the main diagonals, four cases in total, and then determine the other flips according to the colors of the other elements on the diagonals. Finally, check whether everything is white.
Time complexity: `O(4 * N ^ 2)`.

This solution is actually very similar to the light-flipping problems I often did before: flipping one light also flips its four surrounding lights, and the goal is to find the number of steps needed to turn all lights off.
You only need to enumerate the flips in the first row. To change the state of the lights in the current row, we can only flip lights in the next row, so all remaining rows are determined from that.
The problem can be found on LeetCode: [LeetCode 1284. Minimum Number of Flips to Convert Binary Matrix to Zero Matrix](https://leetcode.com/problems/minimum-number-of-flips-to-convert-binary-matrix-to-zero-matrix/description/).
My solution was learned from [acwing](https://www.acwing.com/problem/content/97/). You can also watch Da Xue Cai's video to learn it.

#### Another Solution

Convert this problem into a variant of [2-coloring](https://en.wikipedia.org/wiki/Graph_coloring#Vertex_coloring). Each cell is shared by two lines. If the cell is white, exactly one of the two lines needs to be flipped. If the cell is black, either neither is flipped, or both are flipped.
We treat each diagonal as a node in the graph, and each cell as an edge.
If the cell is black, the two nodes connected by the corresponding edge must have the same color. Otherwise, they must have different colors.
This 2-coloring graph problem can be solved with DFS.
Enumerate the color of the root, use DFS to determine the colors of its neighbors, and if a neighbor's color has already been determined, just check it. The smaller color class represents the number of flips.

The number of edges is `O(N^2)`, and the DFS solution has complexity `O(|Edges|)`.
The total time complexity is `O(N ^ 2)`.

```cpp
#include <iostream>
#include <memory>
#include <set>
#include <unordered_map>
#include <unordered_set>
#include <vector>

using namespace std;
using ll = long long;

struct Line {
    ll difference;
    bool positive;
    vector<weak_ptr<Line>> same_color_neighbors, different_color_neighbors;
    Line(ll d, bool p) : difference(d), positive(p) {}
};

bool dfs(shared_ptr<Line> root, set<shared_ptr<Line>> &white,
         set<shared_ptr<Line>> &black) {
    if (root == nullptr)
        return false;
    ;
    bool w = white.find(root) != white.end();
    for (auto node : root->different_color_neighbors) {
        if (auto node_shared_ptr = node.lock()) {
            if (white.find(node_shared_ptr) == white.end() &&
                black.find(node_shared_ptr) == black.end()) {
                auto &color = w ? black : white;
                color.insert(node_shared_ptr);
                if (!dfs(node_shared_ptr, white, black))
                    return false;
            } else if (white.find(node_shared_ptr) != white.end()) {
                if (w) {
                    return false;
                }
            } else {
                if (!w) {
                    return false;
                }
            }
        }
    }
    for (auto node : root->same_color_neighbors) {
        if (auto node_shared_ptr = node.lock()) {
            if (white.find(node_shared_ptr) == white.end() &&
                black.find(node_shared_ptr) == black.end()) {
                auto &color = w ? white : black;
                color.insert(node_shared_ptr);
                if (!dfs(node_shared_ptr, white, black))
                    return false;
            } else if (white.find(node_shared_ptr) != white.end()) {
                if (!w)
                    return false;
            } else {
                if (w)
                    return false;
            }
        }
    }

    return true;
}

ll solve(vector<vector<bool>> &puzzle) {
    unordered_map<int, shared_ptr<Line>> positive, negative;
    const int n = puzzle.size();
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            int j_sub_i = j - i;
            int j_add_i = j + i;
            if (positive[j_sub_i] == nullptr) {
                positive[j_sub_i] = make_shared<Line>(j_sub_i, true);
            }
            if (negative[j_add_i] == nullptr) {
                negative[j_add_i] = make_shared<Line>(j_add_i, false);
            }
            if (puzzle[i][j]) { // black
                positive[j_sub_i]->same_color_neighbors.push_back(
                    negative[j_add_i]);
                negative[j_add_i]->same_color_neighbors.push_back(
                    positive[j_sub_i]);
            } else {
                positive[j_sub_i]->different_color_neighbors.push_back(
                    negative[j_add_i]);
                negative[j_add_i]->different_color_neighbors.push_back(
                    positive[j_sub_i]);
            }
        }
    }
    set<shared_ptr<Line>> seen;
    ll ans = 0;
    for (auto &direction : {positive, negative}) {
        for (auto p : direction) {
            auto node = p.second;
            if (seen.find(node) == seen.end()) {
                set<shared_ptr<Line>> white, black;
                white.insert(node);
                if (!dfs(node, white, black)) {
                    return -1;
                }
                ans += min(white.size(), black.size());
                seen.insert(white.begin(), white.end());
                seen.insert(black.begin(), black.end());
            }
        }
    }
    return ans;
}

int main(int argc, char const *argv[]) {
    ll T;
    cin >> T;
    for (int iCase = 1; iCase <= T; ++iCase) {
        ll n;
        cin >> n;
        vector<vector<bool>> pullze(n, vector<bool>(n));
        for (int i = 0; i < n; ++i) {
            string row;
            cin >> row;
            for (int j = 0; j < n; ++j) {
                if (row[j] == '#')
                    pullze[i][j] = true;
                else
                    pullze[i][j] = false;
            }
        }
        ll ans = solve(pullze);
        cout << "Case #" << iCase << ": " << ans << endl;
    }
    return 0;
}
```
Points to pay attention to during implementation:
- How to store and construct the Graph.
- When using DFS, you need to traverse all nodes, because the whole graph can be divided into multiple connected components. Starting DFS from only one root gives incomplete coloring.


## C. Elevanagram

For the small test set, `0 <= A_i <= 20`, we can use backtracking and try every positive/negative sign assignment.

Time complexity: O(A_i ^ 9). With pruning, the complexity will be lower.
Space complexity: O(9).

```cpp
#include <algorithm>
#include <iostream>
#include <numeric>
#include <set>
#include <vector>

using namespace std;
using ll = long long;
ll plus_max;
ll s;

bool backtracking(const vector<ll> &A, ll index, ll plus, ll accu) {
    if (plus > plus_max) {
        return false;
    } else if (plus == plus_max) {
        return ((accu - (s - accu)) % 11) == 0;
    } else if (index == 0) {
        ll need_plus = plus_max - plus;
        if (need_plus > A[0]) {
            return false;
        } else {
            accu += need_plus;
            return ((accu - (s - accu)) % 11) == 0;
        }
    } else {
        ll num = index + 1;
        for (ll i = min(A[index], plus_max - plus); i >= 0; --i) {
            if (backtracking(A, index - 1, plus + i, accu + num * i)) {
                return true;
            }
        }
        return false;
    }
}

void solve(const vector<ll> &A) {
    s = std::accumulate(A.begin(), A.end(), 0);
    plus_max = s / 2 + s % 2;
    s = 0;
    for (int i = 0; i < 9; ++i) {
        s += A[i] * (i + 1);
    }

    if (backtracking(A, 8, 0, 0)) {
        cout << "YES";
    } else {
        cout << "NO";
    }
}

int main() {
    int T;
    cin >> T;
    vector<ll> A(9);
    for (int iCase = 0; iCase < T; ++iCase) {
        for (int i = 0; i < 9; ++i) {
            cin >> A[i];
        }
        cout << "Case #" << iCase + 1 << ": ";
        solve(A);
        cout << endl;
    }
}
```
