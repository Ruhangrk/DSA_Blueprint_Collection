> **Warning: this solution might be incorrect.** I have no verified solution for this problem. The page records the statement only.

# [3109 — K Subset Sums II](https://cses.fi/problemset/task/3109)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Status:** Unsolved (no verified solution)  

---

## Problem Statement

Given `n` integers, consider the sums of all subsets of size exactly `m`. Print the `k` smallest such sums in increasing order.

### Input

- The first line has `n`, `m`, `k`.
- The second line has `x[1..n]`.

### Output

`k` integers in increasing order.

### Constraints

- `1 <= m < n <= 2e5`
- `1 <= k <= min(C(n, m), 2e5)`
- `-1e9 <= x[i] <= 1e9`

### Examples

**Input**
```
5 3 9
-3 1 5 2 0
```

**Output**
```
-2 -1 0 2 3 3 4 6 7
```

---

## What I have

Nothing verified. The sample output is a sorted list of the 9 smallest size-3 sums. I checked a few entries by hand, for example `-3 + 1 + 0 = -2`.

## Key takeaway

No solution yet.
