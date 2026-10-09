# [1190 — Subarray Sum Queries](https://cses.fi/problemset/task/1190)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** segment tree, max subarray sum, Kadane merge  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is an array consisting of `n` integers. Some values of the array will be updated, and after each update, your task is to report the maximum subarray sum in the array.

### Input

The first input line contains integers `n` and `m`: the size of the array and the number of updates. The array is indexed `1, 2, ..., n`.

The next line has `n` integers `x1, x2, ..., xn`: the initial contents of the array.

Then there are `m` lines describing the changes. Each line has two integers `k` and `x`: the value at position `k` becomes `x`.

### Output

After each update, print the maximum subarray sum. Empty subarrays (with sum `0`) are allowed.

### Constraints

- `1 <= n, m <= 2e5`
- `-1e9 <= xi <= 1e9`
- `1 <= k <= n`
- `-1e9 <= x <= 1e9`

### Examples

**Input**
```
5 3
1 2 -3 5 -1
2 6
3 1
2 -2
```

**Output**
```
9
13
6
```

---

## What this problem really is

After every **point update**, report the **maximum contiguous subarray sum** over the whole array (empty allowed → answer at least `0`).

## What to use

| Tool | Why |
|------|-----|
| Segment tree node `{sum, pref, suff, best}` | Classic Kadane merge so parents know the global max subarray |
| Point update | Change one leaf; rebuild path to root |
| `long long` | Sums can be large in magnitude |

## Approach

**Root idea:** store enough info in each segment so you can reconstruct the max subarray of the parent from left and right children alone — not only total sum.

**Mental picture:** Kadane’s “best piece” inside a segment can be (1) entirely in the left half, (2) entirely in the right half, or (3) a suffix of the left glued to a prefix of the right. So each node needs: total `sum`, best prefix, best suffix, and best subarray `best`.

**Data structures — node fields**

For a segment covering some contiguous indices:

- `sum` — sum of all elements  
- `pref` — max prefix sum (from left end)  
- `suff` — max suffix sum (from right end)  
- `best` — max subarray sum inside the segment  

**Merge `L` and `R` into parent `P`**

```
P.sum  = L.sum + R.sum
P.pref = max(L.pref, L.sum + R.pref)
P.suff = max(R.suff, R.sum + L.suff)
P.best = max(L.best, R.best, L.suff + R.pref)
```

Leaf for value `v`: `sum = pref = suff = best = v` (then final answer uses `max(0, root.best)` because empty is allowed).

**Steps**

1. Build the segtree from the initial array with the merge above.
2. For each update `(k, x)`: set leaf `k` to `x`, update ancestors.
3. Print `max(0, tree[root].best)`.

## Walkthrough (on a sample)

Start: `1 2 -3 5 -1`. Whole-array max is `1+2-3+5 = 5` (or `5` alone); empty would be `0`, so answer before updates isn’t printed — only after each update.

Update index `2` → `6`: array `1 6 -3 5 -1`. Best = `1+6-3+5 = 9` → **9**.

Update index `3` → `1`: `1 6 1 5 -1`. Best = `1+6+1+5 = 13` → **13**.

Update index `2` → `-2`: `1 -2 1 5 -1`. Best = `1+5 = 6` → **6**.

At the root, each merge recomputes `best` via `max(left.best, right.best, left.suff + right.pref)`.

## Pitfalls

- Forgetting empty subarray → must print at least `0`.
- Storing only `sum` and `best` without `pref`/`suff` — cannot merge correctly.
- `int` overflow — use `long long`.
- Initializing leaf `best` wrong when `v` is negative (leaf `best = v` is fine; clamp with `0` only at the printed answer).

## Complexity

- Time: `O((n + m) log n)`  
- Space: `O(n)`

## Key takeaway

**Max subarray sum with updates = segtree of `(sum, pref, suff, best)`** with the Kadane-style four-way merge; clamp the root’s `best` with `0` if empty is allowed.
