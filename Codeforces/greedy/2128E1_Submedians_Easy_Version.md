# [2128E1 — Submedians (Easy Version)](https://codeforces.com/problemset/problem/2128/E1)

**Platform:** Codeforces
**Source:** Codeforces Round 1039 (Div. 2)
**Tags:** binary search, data structures, dp, greedy, math
**Difficulty / Rating:** 1900
**Time limit:** 3 seconds
**Memory limit:** 512 megabytes
**Editorial:** [Round 1039 editorial (Problem E1 section)](https://codeforces.com/blog/entry/145080)

---

## Problem Statement

This is the easy version of the problem. The only difference is that in this version, you are asked to find a subarray only for the maximum submedian.

You can make hacks only if both versions of the problem are solved.

An integer `v` is a median of an array `b` of length `m` if and only if:

- `v` is greater than or equal to at least `ceil(m / 2)` elements of the array, and
- `v` is less than or equal to at least `ceil(m / 2)` elements of the array.

For instance:

- the only median of `[9, 3, 7]` is `7`,
- the medians of `[5, 3, 7, 9]` are `5`, `6`, and `7`, and
- the only median of `[2, 2, 2]` is `2`.

You're given an integer `k` and an array `a[1], ..., a[n]` of integers between `1` and `n`.

An integer `v` from `1` to `n` is said to be a submedian if there exists at least one pair of indices `(l, r)` such that

- `1 <= l <= r <= n`,
- `r - l + 1 >= k`,
- `v` is a median of the subarray `[a[l], ..., a[r]]`.

It can be proven that there always exists at least one submedian. Find the maximum submedian `v_max` and any corresponding pair of indices `(l, r)`.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 50000`). The description of the test cases follows.

The first line of each test case contains two integers `n` and `k` (`1 <= k <= n <= 300000`).

The second line of each test case contains `n` integers `a[1], a[2], ..., a[n]` (`1 <= a[i] <= n`).

It is guaranteed that the sum of `n` over all test cases doesn't exceed `300000`.

### Output

For each test case, output three integers `v_max`, `l`, and `r` — the maximum submedian `v_max` and the bounds of a subarray of length at least `k` (`r - l + 1 >= k`) such that `v_max` is one of its medians.

If there are many solutions, you can print any of them.

### Constraints

- `1 <= t <= 50000`
- `1 <= k <= n <= 300000`
- `1 <= a[i] <= n`
- Sum of `n` over all test cases `<= 300000`

### Examples

Input:

```
7
4 3
4 1 2 4
5 2
1 2 3 2 1
5 3
1 2 3 2 1
5 3
1 1 2 5 3
1 1
1
2 1
2 1
4 1
1 2 1 3
```

Output:

```
4 1 4
3 3 4
2 2 4
3 3 5
1 1 1
2 1 2
3 4 4
```

### Note

In the first test case, the subarrays of length at least `k = 3` are

- `(l = 1, r = 3)`: `[4, 1, 2]` whose only median is `2`,
- `(l = 2, r = 4)`: `[1, 2, 4]` whose only median is `2`, and
- `(l = 1, r = 4)`: `[4, 1, 2, 4]` whose medians are `2`, `3`, and `4`.

In the second test case, one possible output is `(l = 3, r = 4)` whose medians are `2` and `3`.

Note that it can be proven that no subarray of length at least `2` admits `4` or `5` as a median.

---

## What this problem really is

Among all values that appear as a median of some subarray of length at least `k`, find the largest one, and report one witnessing subarray.

## What to use

| Tool | Why |
|------|-----|
| Binary search on `v` | The predicate "some submedian is `>= v`" is monotonic. |
| `+1 / -1` mapping | `v` is `>=` enough elements iff the sum of `big[i] = (a[i] >= v ? +1 : -1)` over a window is `>= 0`. |
| Prefix minima | While scanning the right end, the best left end for a long enough window is the position with the smallest prefix sum. |

## Approach

**Root idea (from the editorial):** Binary search the largest `v` such that some subarray of length `>= k` has at least half its elements `>= v`. For the maximum such `v`, that witness subarray automatically also satisfies the "enough elements `<= v`" median side, so `v` is a true submedian.

**Mental picture:** Map the array to hills (`+1` if `a[i] >= v`) and valleys (`-1` otherwise). You need a segment of length at least `k` whose hill-minus-valley score is non-negative. Binary search raises the bar `v` until that score just barely still works.

**How that becomes the method:**
1. Binary search `v` in `1..n`.
2. For a fixed mid value `v`, build `big[i] = +1` if `a[i] >= v`, else `-1`, and its prefix sums `pref`.
3. Scan `r` from left to right. Keep the minimum of `pref[l - 1]` over all `l` with `r - l + 1 >= k` (i.e. over `l - 1 <= r - k`). If `pref[r] >=` that minimum, then `v` is feasible; remember one pair `(l, r)`.
4. The largest feasible `v` is `v_max`. Output `v_max` and the remembered `(l, r)`.

**Why the `small` side is free at the optimum (editorial claim):** If the witness for this maximal `v` failed the `small` sum (`enough elements <= v`), then every element counted as `>= v` would actually be `>= v + 1`, so the same subarray would also certify a submedian `>= v + 1`, contradicting maximality.

**Data structures:**
- Prefix-sum array for `big`.
- A running minimum of eligible prefix values while scanning `r`.

## Walkthrough (on a sample)

First test: `a = [4, 1, 2, 4]`, `k = 3`.

- Try `v = 4`. `big = [+1, -1, -1, +1]`, `pref = [0, 1, 0, -1, 0]`.
- At `r = 4` (0-based end index 3), length `4 >= 3`. Min prefix among `pref[0], pref[1]` (left ends giving length `>= 3`) is `min(0, 1) = 0`, and `pref[4] = 0 >= 0`. Feasible on `[1, 4]`.
- Try `v = 5`: every `big[i] = -1`, no non-negative window. Fail.
- So `v_max = 4` with `(l, r) = (1, 4)`, matching the sample.

## Pitfalls

- **Checking only exact medians during the search:** For the binary-search check you only need the `big` side; the `small` side is guaranteed at the maximum `v`.
- **Forgetting length `>= k`:** A short window with a large value is not allowed.
- **`ceil(m / 2)` vs floor:** The `+1/-1` sum `>= 0` encoding matches "at least half the elements" under this median definition.
- **Large sum of `n`:** Each binary-search step is `O(n)`, so total `O(n log n)` per test is fine under the given sum constraint.

## Complexity

- Time: `O(n log n)` per test for the binary search. Over all tests, `O((sum of n) * log n)`.
- Space: `O(n)`.

## Key takeaway

Binary search the largest `v` with a long-enough subarray whose `+1/-1` "at least `v`" score is non-negative; at that maximum, the other median side comes for free.
