# [3222 — Sliding Window Distinct Values](https://cses.fi/problemset/task/3222)

**Platform:** CSES  
**Source:** CSES Problem Set — Sliding Window Problems  
**Tags:** sliding window, frequency map  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to calculate the number of distinct values in each window of `k` elements, from left to right.

### Input

The first line contains two integers `n` and `k`: the number of elements and the size of the window.

Then there are `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print `n-k+1` values: the numbers of distinct values.

### Constraints

- `1 <= k <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
8 3
1 2 3 2 5 2 2 2
```

**Output**
```
3 2 3 2 2 1
```

---

## What this problem really is

For every segment `[i, i+k)`, report how many different numbers appear.

## What to use

| Tool | Why |
|------|-----|
| `map` / `unordered_map` of frequencies | Track count of each value in the window |
| Distinct counter | `++` when freq goes `0→1`, `--` when `1→0` |
| Two pointers slide | Add `x[i+k-1]`, remove `x[i-1]` |

## Approach

**Root idea:** maintain a frequency table for the current window. The number of keys with positive frequency is the answer; update it only when a value’s count crosses zero.

**Mental picture:** a bag of `k` items. When one item type disappears from the bag, distinct drops by one; when a new type appears, it rises by one.

**How that becomes the method**

1. Insert first `k` elements into `freq`, set `distinct =` number of positive keys.
2. Print `distinct`.
3. For `i = k..n-1`: remove `x[i-k]` (if `--freq == 0`, `distinct--` and erase); add `x[i]` (if `freq` was `0`, `distinct++`); print `distinct`.

**Data structures:** `unordered_map<int,int>` or compress + Fenwick of presence (values up to `1e9` → hashing or coordinate compression on the whole array).

## Walkthrough (on a sample)

Windows of length `3`: `{1,2,3}→3`, `{2,3,2}→2`, `{3,2,5}→3`, …, `{2,2,2}→1`.

## Pitfalls

- Not erasing zero-frequency keys (memory / wrong distinct if you count map size carefully — better track `distinct` explicitly).  
- `O(n^2)` set rebuild per window.

## Complexity

- Time: `O(n)` expected with hash map  
- Space: `O(k)`

## Key takeaway

**Sliding distinct count = freq map + counter** that updates only on `0↔1` transitions.
