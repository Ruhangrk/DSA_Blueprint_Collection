# [2422 — Multiplication Table](https://cses.fi/problemset/task/2422)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** binary search, counting  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Find the middle element when the numbers in an `n x n` multiplication table are sorted in increasing order. It is assumed that `n` is odd.

For example, the `3 x 3` multiplication table is as follows:

```
1 2 3
2 4 6
3 6 9
```

The numbers in increasing order are `[1,2,2,3,3,4,6,6,9]`, so the answer is `3`.

### Input

The only input line has an integer `n`.

### Output

Print one integer: the answer to the task.

### Constraints

- `1 <= n < 1e6` (`n` is odd)

### Examples

**Input**
```
3
```

**Output**
```
3
```

---

## What this problem really is

In the multiset `{ i*j | 1<=i,j<=n }`, find the element at 1-based rank `(n*n+1)/2` (the median, since `n*n` is odd).

## What to use

| Tool | Why |
|------|-----|
| Binary search on value | Monotone: larger `x` ⇒ more table entries `<= x` |
| Count `#{i*j <= x}` | For each row `i`, `min(n, x/i)` entries |
| 64-bit | `n~1e6` → products and counts up to `~1e12` |

## Approach

**Root idea:** the median is the smallest number `x` such that at least `(n*n+1)/2` entries of the table are `<= x`.

**Mental picture:** slide a threshold up through the sorted list of products; stop at the middle position. Counting how many products lie under the threshold is easy row-by-row.

**How that becomes the method**

1. `need = (n*n + 1) / 2`.
2. Binary search `lo = 1`, `hi = n*n`:
   - `count(x) = sum_{i=1..n} min(n, x/i)`.
   - If `count(x) >= need`, try smaller; else raise `lo`.
3. Answer = minimal `x` with `count(x) >= need`.

**Data structures:** none; careful `long long` for `n*n`, `x`, `count`.

## Walkthrough (on a sample)

`n=3`, `need=5`.  
`count(2)=4` (1,2,2,3? wait: row1:3, row2:1, row3:0 → actually min(3,2/1)=2, min(3,2/2)=1, min(3,2/3)=0 → 3).  
`count(3)=5` → median `3`.

## Pitfalls

- Using `int` for `n*n` when `n=1e6`.
- Off-by-one on median index (`(n*n+1)/2` vs `n*n/2`).
- Binary search returning a value that does not appear in the table — the minimal `x` with enough count always is a product that appears.

## Complexity

- Time: `O(n log (n^2)) = O(n log n)`
- Space: `O(1)`

## Key takeaway

Multiplication-table median → binary search value + row-wise `min(n, x/i)` counting.
