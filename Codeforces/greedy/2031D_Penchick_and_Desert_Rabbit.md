# [2031D — Penchick and Desert Rabbit](https://codeforces.com/problemset/problem/2031/D)

**Platform:** Codeforces
**Source:** Codeforces Round 987 (Div. 2)
**Tags:** binary search, data structures, dfs and similar, dp, dsu, greedy, implementation, two pointers
**Difficulty / Rating:** 1700
**Time limit:** 3 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Round 987 editorial (Problem D section)](https://codeforces.com/blog/entry/136260)

---

## Problem Statement

Dedicated to pushing himself to his limits, Penchick challenged himself to survive the midday sun in the Arabian Desert!

While trekking along a linear oasis, Penchick spots a desert rabbit preparing to jump along a line of palm trees. There are `n` trees, each with a height denoted by `a[i]`.

The rabbit can jump from the `i`-th tree to the `j`-th tree if exactly one of the following conditions is true:

- `j < i` and `a[j] > a[i]`: the rabbit can jump backward to a taller tree.
- `j > i` and `a[j] < a[i]`: the rabbit can jump forward to a shorter tree.

For each `i` from `1` to `n`, determine the maximum height among all trees that the rabbit can reach if it starts from the `i`-th tree.

### Input

The first line contains the number of test cases `t` (`1 <= t <= 5e5`). The description of the test cases follows.

The first line of each test case contains a single integer `n` (`1 <= n <= 5e5`) — the number of trees.

The second line of each test case contains `n` integers `a[1], a[2], ..., a[n]` (`1 <= a[i] <= n`) — the height of the trees.

It is guaranteed that the sum of `n` over all test cases does not exceed `5e5`.

### Output

For each test case, output `n` integers. The `i`-th integer should contain the maximum height among all trees that the rabbit can reach if it starts from the `i`-th tree.

### Constraints

- `1 <= t <= 5e5`
- `1 <= n <= 5e5`
- `1 <= a[i] <= n`
- Sum of `n` over all test cases `<= 5e5`

### Examples

Input:

```
5
4
2 3 1 4
5
5 4 3 2 1
4
2 1 1 3
4
1 1 3 1
8
2 4 1 6 3 8 5 7
```

Output:

```
3 3 3 4
5 5 5 5 5
2 2 2 3
1 1 3 3
8 8 8 8 8 8 8 8
```

### Note

In the first test case, the initial heights of trees are `a = [2, 3, 1, 4]`.

- If the rabbit starts from the first tree, it can jump to the third tree as `3 > 1` and `1 < 2`. Then, the rabbit can jump to the second tree as `2 < 3` and `3 > 1`. It can be proved that the rabbit cannot reach the fourth tree; hence, the maximum height of the tree that the rabbit can reach is `a[2] = 3`.
- If the rabbit starts from the fourth tree, it does not need to jump anywhere as it is already at the highest tree.

In the second test case, the rabbit can jump to the first tree regardless of which tree it starts from.

In the fifth test case, if the rabbit starts from the fifth tree, it can jump to the fourth tree. Then the rabbit can jump to the seventh tree and finally reach the sixth tree. Therefore, the maximum height of the tree that the rabbit can reach is `8`.

---

## What this problem really is

From each starting index, find the tallest tree reachable under the jump rules: left only to a taller tree, right only to a shorter tree.

## What to use

| Tool | Why |
|------|-----|
| Prefix max `p[i]` | Tallest tree among indices `1..i`. |
| Suffix min `s[i]` | Shortest tree among indices `i+1..n`. |
| Right-to-left DP | Once `ans[i+1]` is known, `ans[i]` is either the same value or just `p[i]`. |

## Approach

**Root idea (from the editorial):** Process indices from right to left. Tree `n` can always reach a global maximum (jump left to it if needed). For index `i`, either the left segment and the right segment can still connect, so `ans[i] = ans[i+1]`, or they cannot connect, so the rabbit is stuck in `1..i` and the answer is the prefix max.

**Mental picture:** Cut the array between `i` and `i+1`. Let `p[i]` be the tallest tree on the left of the cut, and `s[i]` the shortest tree on the right. If `p[i] > s[i]`, there is a bridge: go from `i` to the tall left tree `x`, jump right to the short right tree `y` (allowed because `a[x] > a[y]`), then reach `i+1`. Crossing the cut merges the two sides into one component. If `p[i] <= s[i]`, every left tree is `<=` every right tree, so no jump can cross the cut.

**How that becomes the method:**
1. Compute `p[i] = max(a[1..i])` for every `i`.
2. Compute `s[i] = min(a[i+1..n])` for every `i` (suffix mins).
3. Set `ans[n] = p[n]` (global max).
4. For `i = n-1` down to `1`:
   - If `p[i] > s[i]`, set `ans[i] = ans[i+1]`.
   - Otherwise set `ans[i] = p[i]`.
5. Print `ans[1..n]`.

**Data structures:**
- Arrays `p[]`, `s[]`, `ans[]` of length `n`.
- One right-to-left pass after the prefix/suffix prep.

## Walkthrough (on a sample)

First test: `a = [2, 3, 1, 4]`.

- Prefix max: `p = [2, 3, 3, 4]`.
- Suffix min of the right side: `s[1] = min(3,1,4) = 1`, `s[2] = min(1,4) = 1`, `s[3] = 4`.
- `ans[4] = 4`.
- `i = 3`: `p[3] = 3`, `s[3] = 4`, and `3 <= 4`, so `ans[3] = 3`.
- `i = 2`: `p[2] = 3`, `s[2] = 1`, and `3 > 1`, so `ans[2] = ans[3] = 3`.
- `i = 1`: `p[1] = 2`, `s[1] = 1`, and `2 > 1`, so `ans[1] = ans[2] = 3`.
- Output: `3 3 3 4`.

## Pitfalls

- **Forgetting that `ans[n]` is the global max:** Tree `n` can jump left to any taller tree.
- **Wrong cut test:** The bridge exists only when the left max is strictly greater than the right min.
- **Computing `s[i]` as `min(a[i..n])`:** It must be the min of the strict right side `a[i+1..n]`.
- **Large `t`:** Constraints force `O(n)` per test; do not rebuild graphs or run BFS from every index.

## Complexity

- Time: `O(n)` per test after a linear prefix/suffix pass. Over all tests, `O(sum of n)`.
- Space: `O(n)`.

## Key takeaway

Scan right to left: if the left max beats the right min across the cut, the two sides share one answer; otherwise the left side is sealed and its answer is its own prefix max.
