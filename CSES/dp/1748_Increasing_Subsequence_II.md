# [1748 — Increasing Subsequence II](https://cses.fi/problemset/task/1748)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, Fenwick/segment tree, LIS counting  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to calculate the number of increasing subsequences it contains. If two subsequences have the same values but in different positions in the array, they are counted separately.

### Input

The first input line has an integer `n`: the size of the array.

The second line has `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print one integer: the number of increasing subsequences modulo `1e9+7`.

### Constraints

- `1 <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
3
2 1 3
```

**Output**
```
5
```

### Note

The increasing subsequences are `[2]`, `[1]`, `[3]`, `[2,3]` and `[1,3]`.

---

## What this problem really is

Count **all** strictly increasing subsequences (every non-empty one). Same values at different indices count as different. `n = 2e5` → need `O(n log n)`, not `O(n^2)`.

## What to use

| Tool | Why |
|------|-----|
| `ways[i]` | Number of increasing subsequences **ending** at index `i` |
| Fenwick / segtree on compressed values | Query sum of `ways[j]` for all `j` with `x[j] < x[i]` and `j < i` |
| Coordinate compression | Values up to `1e9` |
| Modulo `1e9+7` | Counts explode |

## Approach

**Root idea:** every increasing subsequence ending at `i` is either just `[x[i]]`, or some increasing subsequence ending at a earlier `j` with `x[j] < x[i]`, then append `x[i]`. So

```
ways[i] = 1 + sum(ways[j] for j < i and x[j] < x[i])
```

Total answer = sum of all `ways[i]` (each subsequence counted at its end).

**Mental picture:** process the array left → right. Before handling `x[i]`, a tree already stores, keyed by value, how many increasing subsequences end with that value so far. Query everything strictly below `x[i]`, add `1`, then point-add that into the tree at value `x[i]`.

**Data structures**

- Compress `x` values to ranks `1..n`.
- Fenwick/segtree storing modular sums over ranks.
- Process indices `0 .. n-1` in order.

**Steps**

1. Compress values.
2. `ans = 0`. For each `x` left to right:
   - `w = (1 + query_sum(ranks_strictly_less_than_x)) % MOD`
   - `ans = (ans + w) % MOD`
   - `update(rank(x), w)`
3. Print `ans`.

## Walkthrough (on a sample)

`[2, 1, 3]`

| i | x | ways[i] | why |
|---|---|---------|-----|
| 0 | 2 | 1 | `[2]` |
| 1 | 1 | 1 | `[1]` (nothing smaller before) |
| 2 | 3 | 1+1+1 = 3 | `[3]`, `[2,3]`, `[1,3]` |

Sum = `1+1+3 = 5`.

## Pitfalls

- **Non-strict** — equal values must **not** extend; query `<`, not `<=`.
- **Order of update** — update after querying so you don’t use the same index twice.
- **`n = 2e5`** — nested loops TLE; tree is required.
- Single elements count — the `+1` in `ways[i]`.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Count increasing subsequences:** `ways[i] = 1 + sum of ways with smaller value on the left`, maintained with a Fenwick/segment tree on compressed values.
