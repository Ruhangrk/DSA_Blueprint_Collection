# [1749 — List Removals](https://cses.fi/problemset/task/1749)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** Fenwick / segment tree, order statistics  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a list consisting of `n` integers. Your task is to remove elements from the list at given positions, and report the removed elements.

### Input

The first input line has an integer `n`: the initial size of the list. During the process, the elements are numbered `1, 2, ..., k` where `k` is the current size of the list.

The second line has `n` integers `x1, x2, ..., xn`: the contents of the list.

The last line has `n` integers `p1, p2, ..., pn`: the positions of the elements to be removed.

### Output

Print the elements in the order they are removed.

### Constraints

- `1 <= n <= 2e5`
- `1 <= xi <= 1e9`
- `1 <= pi <= n-i+1`

### Examples

**Input**
```
5
2 6 1 4 2
3 1 3 1 1
```

**Output**
```
1 2 2 6 4
```

### Note

The contents of the list are `[2,6,1,4,2]`, `[2,6,4,2]`, `[6,4,2]`, `[6,4]`, `[4]` and `[]`.

---

## What this problem really is

Start with a list. Repeatedly delete the `p`-th **remaining** element (1-based among survivors) and print its value. Need faster than `O(n^2)` erases.

## What to use

| Tool | Why |
|------|-----|
| Fenwick/segtree of counts | `1` = still present at original index |
| Find-by-order | Locate the `p`-th living index via prefix sums |
| Point delete | Set count at that index to `0` |

## Approach

**Root idea:** keep original positions `1..n`. A BIT of `0/1` “alive” flags lets you binary-lift / binary-search the smallest index whose alive-prefix sum equals `p`.

**Mental picture:** people in a line; some already left. Position `p` means “skip `p-1` living people.”

**Data structures**

- Array `x[1..n]` fixed.
- Fenwick `bit` init `1` at every index (all alive).

**For each removal `p`**

1. Find smallest `idx` with `bit.sum(idx) == p` (binary search on fenwick).
2. Print `x[idx]`.
3. `bit.add(idx, -1)` (mark removed).

## Walkthrough (on a sample)

List `2 6 1 4 2`. Remove pos `3` → `1`; then `1` → `2`; then `3` → `2`; then `1` → `6`; then `1` → `4`.

## Pitfalls

- Interpreting `p` as a fixed original index — it’s among **current** elements.
- `vector::erase` each time — TLE.
- Off-by-one in order-statistic search.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Delete k-th remaining = order-statistic on a 0/1 Fenwick/segtree** over original positions.
