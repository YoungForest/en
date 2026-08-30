---
title: Using pair as the Key of unordered_map and unordered_set in C++
date: 2020-05-27 11:57:01
tags:
- C++
- hash
- unordered_map
categories:
- Programming
description: "A collision-prone pair hash caused TLE, leading me back to hash_combine and a broader lesson about copied snippets versus understanding classic references."
translations:
  zh-CN: https://youngforest.github.io/2020/05/27/unordered-map-hash-pair-c/
  en: https://youngforest.github.io/en/2020/05/27/unordered-map-hash-pair-c/
---
Today, while solving [an AtCoder problem](https://atcoder.jp/contests/abc168/tasks/abc168_e), one test case kept getting TLE. I studied the difference between that test case and the others, but could not figure it out no matter how hard I thought. Later, I replaced `unordered_map` with `map` and it passed. Although the difference between HashMap and TreeMap is not large on small datasets, HashMap should still be better when the data volume is large. So the best practice is to use a HashMap when ordering is not needed.

I had also never encountered a case before where HashMap performed so much worse than TreeMap. After spending the whole morning on it, I finally located the issue: my hash function implementation for `pair` was terrible. Since the C++ STL does not provide a hash specialization for `pair`, if you want to use `pair` as a key in `unordered_map`, you need to implement the hash function yourself. I had directly copied an implementation from the internet: `std::hash<T>()(pair.first) ^ std::hash<U>()(pair.second)`. To avoid misleading anyone, I will not paste that code here. This copied implementation really hurt me: the hash function had severe collisions, causing very low efficiency. Surprisingly, this wrong implementation is everywhere online, both in Chinese and in English. I only found the root cause and the correct implementation in an obscure corner, namely the comment section of a [Stack Overflow question](https://stackoverflow.com/questions/20590656/error-for-hash-function-of-pair-of-ints). So I am summarizing it in this post to help more people avoid the pitfall.

<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/unordered-map-hash-pair-c/en-hero.webp" alt="Swapped pairs of geometric tokens jam one symmetric sorting chute while a better mixer distributes each combination across separate bins" width="1536" height="864" decoding="async" fetchpriority="high">
</figure>

<!-- more -->

>std::hash<T>()(x.first) ^ std::hash<T>()(x.second); - that's a spectacularly collision-prone way to hash a pair, as every pair with two identical value hashes to 0, and every pair {a, b} hashes the same as {b, a}. For vaguely demanding use, much better to find a hash_combine function and employ that.

<figure class="editorial-illustration">
  <img src="/en/images/ai/unordered-map-hash-pair-c/en-hash-combine.webp" alt="An old unlabelled workshop manual unfolds into a layered mixing mechanism that turns two components into several distinct key patterns" width="1536" height="864" loading="lazy" decoding="async">
</figure>

What surprised me was that as soon as I saw this comment, it hit me like an electric shock. I suddenly remembered that many years ago, when I was still a beginner, the author of *The C++ Standard Library, 2nd Edition* had already given an excellent solution. I hurriedly dug out my cherished copy of *The C++ Standard Library, 2nd Edition*, turned to the `unordered_map` chapter, "7.9.2 Creating and Controlling Unordered Container", and copied out the template for hashing arbitrary structures:

```cpp
#include <functional>
// from boost (functional/hash):
// see http://www.boost.org/doc/libs/1_35_0/doc/html/hash/combine.html template
template <typename T>
inline void hash_combine(std::size_t &seed, const T &val) {
    seed ^= std::hash<T>()(val) + 0x9e3779b9 + (seed << 6) + (seed >> 2);
}
// auxiliary generic functions to create a hash value using a seed
template <typename T> inline void hash_val(std::size_t &seed, const T &val) {
    hash_combine(seed, val);
}
template <typename T, typename... Types>
inline void hash_val(std::size_t &seed, const T &val, const Types &... args) {
    hash_combine(seed, val);
    hash_val(seed, args...);
}

template <typename... Types>
inline std::size_t hash_val(const Types &... args) {
    std::size_t seed = 0;
    hash_val(seed, args...);
    return seed;
}

struct pair_hash {
    template <class T1, class T2>
    std::size_t operator()(const std::pair<T1, T2> &p) const {
        return hash_val(p.first, p.second);
    }
};

#include <bits/stdc++.h>
using namespace std;
using ll = long long;

int main() {
    unordered_map<pair<ll, ll>, ll, pair_hash> slopeCount;
    unordered_set<pair<ll, ll>, pair_hash> seen;
    return 0;
}
```

There is also a tuple version of the hash implementation. I updated my answer on [Stack Overflow](https://stackoverflow.com/a/62035742/6393787). Anyone who needs it can take it and give it an upvote.

After modifying the `hash_pair` implementation, I finally got AC as expected. A single hash function bug cost me an entire morning, and it also made me benefit from learning I had done many years ago. Back then, when I was grinding through *The C++ Standard Library*, I did not pay special attention to this piece of code. This shows that reading more books is never a bad thing.

Because Google Search is so convenient in daily work, whenever I run into a problem I tend to simply copy and paste. Usually, the problem gets solved. This does make tasks faster and often works well. But the habit of not digging deeper is very bad for my own growth. So when there is enough time, I need to study problems more deeply and read more classic books. Many problems and solutions have already been given in classic books. Reading also inspires deeper thinking and growth. This experience with using `pair` in `unordered_map` is the best proof.
