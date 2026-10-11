# [1157 — Mex Grid Queries](https://cses.fi/problemset/task/1157)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Math, XOR, mex, pattern discovery  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider an infinite grid with 1-indexed rows and columns. Each cell gets the smallest non-negative integer that does not appear to its left in the same row, and does not appear above it in the same column (the mex of those values).

Given `(y, x)`, print the value in that cell.

### Input

- One line with `y` and `x`.

### Output

Print one integer: the value at `(y, x)`.

### Constraints

- `1 <= y, x <= 1e9`

### Examples

**Input**
```
3 5
```

**Output**
```
6
```

---

## What this problem really is

The grid is far too large to build, so we need a formula. Work with 0-indexed coordinates `a = y - 1` and `b = x - 1`. The grid turns out to be the **XOR table**: the value at `(a, b)` is `a ^ b`, where `^` is bitwise XOR in C++.

## What to use

| Tool | Why |
|------|-----|
| 0-indexed coordinates | XOR works on 0-based indices, so `a = y-1`, `b = x-1` |
| Bitwise XOR (`^`) | Gives the value at each cell in O(1) |
| Small hand-built grid | Confirms the pattern before trusting it |

## Approach

**Root idea:** the grid has the same structure as the XOR addition table. With `a` and `b` 0-indexed, the value at `(a, b)` is `a ^ b`.

**Mental picture:** think of the row `a` as a list of numbers. Walking left along row `a`, we see the values `a ^ 0, a ^ 1, ..., a ^ (b-1)`. Walking up column `b`, we see `0 ^ b, 1 ^ b, ..., (a-1) ^ b`. The mex of these two lists is `a ^ b`. So the grid never repeats a value in a row or column, and each cell takes exactly the one value its neighbours have not used.

**Why the formula holds:** this is the standard nim-sum table. Both lists together miss exactly the number `a ^ b`, and every smaller number appears in one of them. The reasoning is bit-by-bit, but for this page the important part is checking it on a small grid.

**Check on a 3 by 3 grid (0-indexed):**

```
row 0:  0 1 2
row 1:  1 0 3
row 2:  2 3 0
```

- Cell `(1, 1)`: left `1`, above `1`. Mex is `0`, and `1 ^ 1 = 0`.
- Cell `(1, 2)`: left `1, 0`, above `2`. Mex is `3`, and `1 ^ 2 = 3`.
- Cell `(2, 2)`: left `2, 3` (row 2), above `2, 3` (column 2). The set is `{2, 3}`, mex is `0`, and `2 ^ 2 = 0`.

The pattern holds on every cell checked.

**Method**

1. Read `y` and `x`.
2. Compute `a = y - 1`, `b = x - 1`.
3. Print `a ^ b`.

## Walkthrough (on the sample)

Input `y = 3`, `x = 5`.

- `a = 3 - 1 = 2`, which is `010` in binary.
- `b = 5 - 1 = 4`, which is `100` in binary.
- `a ^ b = 010 ^ 100 = 110 = 6`.

Output: `6`. Matches the expected output.

## Pitfalls

- Forgetting to convert to 0-based. Using `y ^ x` directly gives the wrong answer.
- Using `pow` or building the grid. The grid is up to `1e9 x 1e9`, so only the formula is possible.
- Using `int` for the XOR. The values are up to `1e9`, and `long long` is safe.

## Complexity

- Time: `O(1)`.
- Space: `O(1)`.

## Key takeaway

Mex grids built from "not in row to the left and not in column above" are XOR tables. Convert to 0-based coordinates, then the answer is one XOR operation. Verify the pattern on a small grid before relying on it.
