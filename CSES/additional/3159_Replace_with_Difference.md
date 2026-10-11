> **Warning: this solution might be incorrect.** I have no verified solution for this problem. The page records the statement only.

# [3159 — Replace with Difference](https://cses.fi/problemset/task/3159)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Status:** Unsolved (no verified solution)  

---

## Problem Statement

You are given an array of `n` integers. Perform `n-1` operations: choose two numbers `a` and `b`, remove both, and insert `|a - b|`. The last number left must be `0`. Print the chosen pairs, or `-1` if no sequence exists.

### Input

- The first line has `n`.
- The second line has `x[1..n]`.

### Output

`n-1` lines with `a b`, or a single `-1`.

### Constraints

- `2 <= n <= 1000`
- `1 <= x[i] <= 1000`

### Examples

**Input**
```
5
2 7 4 12 1
```

**Output** (one valid answer)
```
2 12
7 10
4 1
3 3
```

---

## What I have

Nothing verified. The sample output is one valid sequence, which I checked by hand: each step removes a pair and inserts their absolute difference, and the final value is `0`.

## Key takeaway

No solution yet.
