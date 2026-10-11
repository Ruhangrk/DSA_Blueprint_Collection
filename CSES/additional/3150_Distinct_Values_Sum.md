# [3150 — Distinct Values Sum](https://cses.fi/problemset/task/3150)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** contribution technique, arrays  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array `x1,x2,...,xn`. Let `d(a,b)` denote the number of distinct values in the subarray `xa, x{a+1}, ..., xb`.

Your task is to calculate the sum `sum_{a=1..n} sum_{b=a..n} d(a,b)`, i.e., the sum of `d(a,b)` for all subarrays.

### Input

The first line has an integer `n`: the array size.

The next line has `n` integers `x1,x2,...,xn`: the array contents.

### Output

Print one integer: the required sum.

### Constraints

- `1 <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
5
1 2 3 1 1
```

**Output**
```
29
```

Explanation: 6 subarrays have 1 distinct value, 4 have 2, and 5 have 3 → `6*1 + 4*2 + 5*3 = 29`.

---

## What this problem really is

Sum, over all subarrays, of the number of distinct elements in that subarray — without iterating all `O(n^2)` subarrays.

## What to use

| Tool | Why |
|------|-----|
| Contribution of each index | Count how many subarrays “use” this occurrence as a new distinct value |
| Previous occurrence | An occurrence at `i` is first in the subarray iff the left end is after `prev[i]` |
| 64-bit ints | Sum is `O(n^3)` worst-case magnitude-wise but fits in 64-bit for `n=2e5` |

## Approach

**Root idea:** `d(a,b)` counts how many values have at least one occurrence in `[a,b]`. Flip: for each occurrence at index `i`, count subarrays where this occurrence is the **leftmost** occurrence of value `x[i]` inside the subarray — then that occurrence contributes `1` to `d` for each such subarray.

**Mental picture:** each time a value “enters” a subarray for the first time (at its leftmost copy inside), it scores one point. Sum points over all subarrays.

**How that becomes the method**

1. For each index `i` (`1`-based), let `prev[i]` = previous index with the same value, or `0` if none (`map` / coordinate compress + last array).
2. Contribution of `i`: left endpoint in `prev[i]+1 .. i`, right endpoint in `i .. n`:  
   `add = (i - prev[i]) * (n - i + 1)`.
3. `ans += add` (use `long long`).
4. Print `ans`.

**Data structures:** `unordered_map` or `map` for last position; `long long ans`.

## Walkthrough (on a sample)

`[1,2,3,1,1]`, `n=5`.

| i | x | prev | contrib `(i-prev)*(n-i+1)` |
|---|---|------|------------------------------|
| 1 | 1 | 0 | `1*5=5` |
| 2 | 2 | 0 | `2*4=8` |
| 3 | 3 | 0 | `3*3=9` |
| 4 | 1 | 1 | `3*2=6` |
| 5 | 1 | 4 | `1*1=1` |

Sum `5+8+9+6+1=29`.

## Pitfalls

- Using next occurrence instead of previous (wrong side).
- 32-bit overflow.
- Off-by-one in `prev` when value never appeared (`prev=0`).

## Complexity

- Time: `O(n)` expected with hash map
- Space: `O(n)`

## Key takeaway

Sum of distinct-counts over subarrays = sum over indices of `(i - prev[i]) * (n - i + 1)`.
