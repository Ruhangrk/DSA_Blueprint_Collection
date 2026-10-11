# [2121F — Yamakasi](https://codeforces.com/problemset/problem/2121/F)

**Platform:** Codeforces
**Source:** Codeforces Round 1032 (Div. 3)
**Tags:** binary search, brute force, data structures, greedy, two pointers
**Difficulty / Rating:** 1800
**Time limit:** 3 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Round 1032 editorial (Problem F section)](https://codeforces.com/blog/entry/143822)

---

## Problem Statement

You are given an array of integers `a[1], a[2], ..., a[n]` and two integers `s` and `x`. Count the number of subsegments of the array whose sum of elements equals `s` and whose maximum value equals `x`.

More formally, count the number of pairs `1 <= l <= r <= n` such that:

- `a[l] + a[l + 1] + ... + a[r] = s`.
- `max(a[l], a[l + 1], ..., a[r]) = x`.

### Input

Each test consists of multiple test cases. The first line contains a single integer `t` (`1 <= t <= 1e4`) — the number of test cases. The description of the test cases follows.

The first line of each test case contains three integers `n`, `s`, and `x` (`1 <= n <= 2e5`, `-2e14 <= s <= 2e14`, `-1e9 <= x <= 1e9`).

The second line of each test case contains `n` integers `a[1], a[2], ..., a[n]` (`-1e9 <= a[i] <= 1e9`).

It is guaranteed that the sum of `n` across all test cases does not exceed `2e5`.

### Output

For each test case, output the number of subsegments of the array whose sum of elements equals `s` and whose maximum value equals `x`.

### Constraints

- `1 <= t <= 1e4`
- `1 <= n <= 2e5`
- `-2e14 <= s <= 2e14`
- `-1e9 <= x <= 1e9`
- `-1e9 <= a[i] <= 1e9`
- Sum of `n` across all test cases `<= 2e5`

### Examples

Input:

```
9
1 0 0
0
1 -2 -1
-2
3 -1 -1
-1 1 -1
6 -3 -2
-1 -1 -1 -2 -1 -1
8 3 2
2 2 -1 -2 3 -1 2 2
9 6 3
1 2 3 1 2 3 1 2 3
13 7 3
0 -1 3 3 3 -2 1 2 2 3 -1 0 3
2 -2 -1
-2 -1
2 -2 -1
-1 -2
```

Output:

```
1
0
2
0
2
7
8
0
0
```

### Note

In the first test case, the suitable subsegment is `l = 1`, `r = 1`.

In the third test case, the suitable subsegments are `l = 1`, `r = 1` and `l = 3`, `r = 3`.

In the fifth test case, the suitable subsegments are `l = 1`, `r = 3` and `l = 6`, `r = 8`.

In the sixth test case, the suitable subsegments are those for which `r = l + 2`.

In the seventh test case, the following subsegments are suitable:

- `l = 1`, `r = 7`
- `l = 2`, `r = 7`
- `l = 3`, `r = 6`
- `l = 4`, `r = 8`
- `l = 7`, `r = 11`
- `l = 7`, `r = 12`
- `l = 8`, `r = 10`
- `l = 9`, `r = 13`

---

## What this problem really is

Count subarrays with a fixed sum `s` whose maximum is exactly a fixed value `x`.

## What to use

| Tool | Why |
|------|-----|
| Prefix sums | Subarray sum `a[l..r]` becomes `pref[r] - pref[l - 1]`. |
| Frequency map of prefix sums | While scanning the right end `r`, count how many left ends give sum `s`. |
| Barrier pointer `lef` | Tracks the leftmost index still allowed inside the current segment (no value `> x` since the last clear). |

## Approach

**Root idea (from the editorial):** First ignore the maximum and count subarrays with sum `s` using prefix sums and a map. Then enforce `max = x` by only counting left ends that sit after the last value `> x`, and only adding those left ends into the map after an occurrence of `x` has been seen in the current segment.

**Mental picture:** Walk the right end `r` from left to right. Values `> x` are brick walls: they wipe the map and restart the segment after the wall. Values equal to `x` are doors: when you hit one, every still-pending left start inside the current segment becomes legal, because that subarray now contains an `x` and still contains nothing larger. Values `< x` just extend the current segment; they do not unlock new left starts.

**How that becomes the method:**
1. Build prefix sums `pref[0..n]` with `pref[0] = 0`.
2. Keep a map `cnt` from prefix-sum value to frequency, initially empty, and a pointer `lef = 1`.
3. For `r = 1..n`:
   - If `a[r] > x`: clear `cnt` and set `lef = r + 1`.
   - Else if `a[r] == x`: while `lef <= r`, do `cnt[pref[lef - 1]]++` and `lef++`.
   - Else (`a[r] < x`): do nothing to the map.
   - Add `cnt[pref[r] - s]` to the answer.
4. Print the answer.

Why this forces `max = x`:
- Nothing larger than `x` can appear in a counted subarray, because walls clear the map.
- Every left end currently in the map was inserted when some index `p` with `a[p] = x` was processed, and that `p` lies between `l` and the current `r`, so the maximum is at least `x`. Combined with the previous point, the maximum is exactly `x`.

**Data structures:**
- Prefix-sum array `pref[]`.
- Ordered / hash map `cnt` of prefix-sum frequencies.
- Integer pointer `lef`.

## Walkthrough (on a sample)

Fifth test: `n = 8`, `s = 3`, `x = 2`, `a = [2, 2, -1, -2, 3, -1, 2, 2]`.

- `r = 1`, `a = 2 = x`: insert `pref[0] = 0`, then `lef = 2`. `pref[1] - s = 2 - 3 = -1`, count `0`.
- `r = 2`, `a = 2 = x`: insert `pref[1] = 2`, `lef = 3`. `pref[2] - s = 4 - 3 = 1`, count `0`.
- `r = 3`, `a = -1 < x`: map unchanged. `pref[3] - s = 3 - 3 = 0`, count `1` (left end `1`). Subarray `[2, 2, -1]` sums to `3` and max is `2`.
- `r = 4`, `a = -2 < x`: `pref[4] - s = 1 - 3 = -2`, count `0`.
- `r = 5`, `a = 3 > x`: clear map, `lef = 6`.
- `r = 6`, `a = -1 < x`: map empty, count `0`.
- `r = 7`, `a = 2 = x`: insert `pref[5] = 1` and `pref[6] = 0`, `lef = 8`. `pref[7] - s = 2 - 3 = -1`, count `0`.
- `r = 8`, `a = 2 = x`: insert `pref[7] = 2`, `lef = 9`. `pref[8] - s = 4 - 3 = 1`, count `1` (left end `6`). Subarray `[-1, 2, 2]` sums to `3` and max is `2`.

Total answer `2`, matching the sample.

## Pitfalls

- **Adding every left end immediately:** If you insert left ends before seeing an `x`, you also count subarrays whose max is `< x`.
- **Forgetting to clear on `a[r] > x`:** One value larger than `x` invalidates every open left end.
- **64-bit sums:** Prefix sums and `s` need 64-bit integers; `s` can be as large as `2e14` in absolute value.
- **Map key `pref[r] - s`:** Look up the needed previous prefix, not `pref[r] + s`.

## Complexity

- Time: `O(n log n)` per test with a tree map (each index is inserted at most once). Over all tests, `O(sum of n * log n)`.
- Space: `O(n)`.

## Key takeaway

Count sum-`s` subarrays with prefix frequencies, but only unlock left ends after an `x` appears, and wipe everything when a value larger than `x` appears.
