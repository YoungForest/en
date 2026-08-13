---
title: Kick Start 2020 Round C
date: 2020-05-18 10:55:44
tags:
- Competitive Programming
categories:
- KickStart
translations:
  zh-CN: https://youngforest.github.io/2020/05/18/kick-start-2020-round-C/
  en: https://youngforest.github.io/en/2020/05/18/kick-start-2020-round-C/
---
| ID | score | rank | Bike Tour | Bus Routes | Robot Path Coding | Wandering Robot | Time |
|--|--|--|--|--|--|--|--|
| YoungForest | 74 | 524 | 5 + 7 | 10 + 13 | 11 + 16 | 14 + 0 | 1:35:18 |

Last month, because my Round B result was decent, I received a "Congraduation" email from Google CN HR. This month, I will keep going and work hard toward the dream of joining Google.

## A. Countdown

One pass. Find the countdown pattern. Since the starting number must be `K`, if the countdown fails midway, we can continue searching directly from the failure position.

Time complexity: O(N),
space complexity: O(N).

```cpp
#include <bits/stdc++.h>

using namespace std;

int main() {
    int T;
    cin >> T;

    for (int iCase = 1; iCase <= T; ++iCase) {
        int N, K;
        cin >> N >> K;
        vector<int> mountains(N);
        for (int i = 0; i < N; ++i) {
            cin >> mountains[i];
        }
        int ans = 0;
        for (int i = 0; i < N; ) {
            if (mountains[i] == K) {
                int need = K - 1;
                int j = 1;
                while (i + j < N && need >= 1 && mountains[i+j] == need) {
                    ++j;
                    --need;
                }
                if (need == 0) ++ans;
                i = i + j;
            } else {
                ++i;
            }
        }
        cout << "Case #" << iCase << ": " << ans << endl;
    }

    return 0;
}
```

## B. Stable Wall

The problem statement itself is fairly hard to understand. But in essence, it is a topological sorting problem: the blocks below must be placed first.
There is one corner case to note: the case with only one row.
In my first version of the code, `seen` was updated when judging the upper-lower relationship. If there was only one row, the letters in the first row would be ignored.
Moving the `seen` update out separately fixes it.

Time complexity: O(R * C * 26),
space complexity: O(R * C + 26).

```cpp
#include <bits/stdc++.h>

using namespace std;

int main() {
    int T;
    cin >> T;

    for (int iCase = 1; iCase <= T; ++iCase) {
        int R, C;
        cin >> R >> C;
        vector<string> rows(R);
        for (int i = 0; i < R; ++i) {
            cin >> rows[i];
        }
        string ans;
        // 拓扑排序
        unordered_map<char, unordered_set<char>> out;
        unordered_map<char, unordered_set<char>> in;
        unordered_set<char> seen;
        queue<char> zeroIn;
        for (int i = 0; i < R; ++i) {
            for (int j = 0; j < C; ++j) {
                seen.insert(rows[i][j]);
            }
        }
        for (int i = 0; i + 1 < R; ++i) {
            for (int j = 0; j < C; ++j) {
                char down = rows[i + 1][j];
                char up = rows[i][j];
                if (down == up)
                    continue;
                out[down].insert(up);
                in[up].insert(down);
            }
        }
        for (char c : seen) {
            if (in[c].empty()) {
                zeroIn.push(c);
            }
        }
        while (!zeroIn.empty()) {
            char base = zeroIn.front();
            zeroIn.pop();
            ans.push_back(base);
            for (char up : out[base]) {
                in[up].erase(base);
                if (in[up].empty()) {
                    zeroIn.push(up);
                }
            }
        }
        cout << "Case #" << iCase << ": "
             << (ans.size() == seen.size() ? ans : "-1") << endl;
    }

    return 0;
}
```

## C. Perfect Subarray

Use prefix sums to calculate subarray sums quickly. Then use a HashMap to store previous prefix sums. For the current prefix sum, enumerate all square numbers and search for previous prefix sums. Since this problem is sensitive to constant factors, some competitive programming tricks are needed.
For example:

- `unordered_map` can use `reserve` to expand directly to the maximum possible size;
- use an array instead of a HashMap, and when indices can be negative, shift all indices by a constant.

Time complexity: O(N * sqrt(MAXSUM)) = O(N * 3000),
space complexity: O(N + MAXSUM).

```cpp
#include <bits/stdc++.h>

using namespace std;

using ll = long long;
const ll MAXSQRT = 3163;
const int mxN = 1e5 + 5;
int seen[2*110*mxN];

int main() {
    int T;
    cin >> T;
    const int mid = 105 * mxN + 2;
    for (int iCase = 1; iCase <= T; ++iCase) {
        int N;
        cin >> N;
        ll ans = 0;
        int presum = 0;
	    memset(seen, 0, sizeof seen);
        seen[presum + mid] = 1;
        for (int i = 0; i < N; ++i) {
            int A;
            cin >> A;
            presum += A;
            for (int squareI = 0; squareI <= MAXSQRT; ++squareI) {
                ans += seen[mid + presum - squareI*squareI];
            }
            ++seen[mid + presum];
        }
        cout << "Case #" << iCase << ": " << ans << endl;
    }

    return 0;
}
```

## D. Candies

There are two types of operations in the problem: querying an interval sum and updating one value. This is very suitable for a segment tree. Because the definition of sweet score is different from an ordinary sum, we can compute the sweet score with two segment trees. They respectively maintain `+1, -1, +1, ...` and `+1, -2, +3, ...`. Then during `queryRange`, subtract a multiple of the former from the latter, and finally multiply by `+1`/`-1`.

Here, thanks to [Hua Hua's segment tree template](https://www.youtube.com/watch?v=rYBtViWXYeI), I was able to quickly AC this problem.

Time complexity: O(N log N),
space complexity: O(N).

```cpp
#include <bits/stdc++.h>

using namespace std;

using ll = long long;

class SegmentTreeNode {
private:
    unique_ptr<SegmentTreeNode> left = nullptr, right = nullptr;
    ll start = 0, end = 0;
    ll val = 0;
public:
    SegmentTreeNode(ll value, ll start_, ll end_): val(value), start(start_), end(end_) {}
    SegmentTreeNode(ll start_, ll end_): start(start_), end(end_) {}
    static unique_ptr<SegmentTreeNode> buildTree(const vector<ll>& nums, ll i, ll j) {
        if (i == j) {
            return make_unique<SegmentTreeNode>(nums[i], i, j);
        } else {
            ll mid = i + (j - i) / 2;
            auto ret = make_unique<SegmentTreeNode>(i, j);
            ret->left = buildTree(nums, i, mid);
            ret->right = buildTree(nums, mid + 1, j);
            ret->val = ret->left->val + ret->right->val;
            return ret;
        }
    }
    ll queryRange(ll i, ll j) const {
        if (i == start && j == end) {
            return val;
        } else {
            ll mid = start + (end - start) / 2;
            if (j <= mid) {
                return left->queryRange(i, j);
            } else if (i > mid) {
                return right->queryRange(i, j);
            } else {
                return left->queryRange(i, mid) + right->queryRange(mid + 1, j);
            }
        }
    }
    void update(ll index, ll value) {
        if (start == index && index == end) {
            val = value;
        } else {
            ll mid = start + (end - start) / 2;
            if (mid >= index) {
                left->update(index, value);
            } else {
                right->update(index, value);
            }
            val = left->val + right->val;
        }
    }
};

int main() {
    ll T;
    cin >> T;

    for (ll iCase = 1; iCase <= T; ++iCase) {
        ll N, Q;
        cin >> N >> Q;
        vector<ll> nums(N);
        for (ll i = 0; i < N; ++i) {
            cin >> nums[i];
        }
        for (ll i = 0; i < N; ++i) {
            if (i % 2 == 1)
                nums[i] *= -1;
        }
        auto positiveNegtive = SegmentTreeNode::buildTree(nums, 0, nums.size() - 1);
        for (ll i = 0; i < N; ++i) {
            nums[i] *= (i+1);
        }
        auto multiTree = SegmentTreeNode::buildTree(nums, 0, nums.size() - 1);
        ll ans = 0;
        for (ll q = 0; q < Q; ++q) {
            string op;
            cin >> op;
            ll start, end;
            cin >> start >> end;
            if (op == "Q") {
                ans += ((start % 2 == 0) ? -1 : 1 ) * (multiTree->queryRange(start-1,end -1) - (start - 1) * positiveNegtive->queryRange(start-1, end-1));
            } else {
                ans += 0;
                positiveNegtive->update(start-1, ((start % 2 == 0) ? -1 : 1) * end);
                multiTree->update(start-1, ((start % 2 == 0) ? -1 : 1) * start * end);
            }
        }

        cout << "Case #" << iCase << ": " << ans << endl;
    }

    return 0;
}
```
