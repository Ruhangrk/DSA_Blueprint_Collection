# [2074 — Reversals and Sums](https://cses.fi/problemset/task/2074)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** treap, lazy reverse, subtree sum  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, you have to process following operations:

1. reverse a subarray
2. calculate the sum of values in a subarray

### Input

The first input line has two integers `n` and `m`: the size of the array and the number of operations. The array elements are numbered `1,2,...,n`.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the array.

Finally, there are `m` lines that describe the operations. Each line has three integers `t`, `a` and `b`. If `t=1`, you should reverse a subarray from `a` to `b`. If `t=2`, you should calculate the sum of values from `a` to `b`.

### Output

Print the answer to each operation where `t=2`.

### Constraints

- `1 <= n <= 2e5`
- `1 <= m <= 1e5`
- `0 <= xi <= 1e9`
- `1 <= a <= b <= n`

### Examples

**Input**
```
8 3
2 1 3 4 5 3 4 4
2 2 4
1 3 6
2 2 4
```

**Output**
```
8
9
```

---

## What this problem really is

Same sequence treap as substring reversals, but nodes hold integers and you must answer **range sums** as well as range reverses.

## What to use

| Tool | Why |
|------|-----|
| Implicit treap | Position-based split/merge |
| Lazy reverse | Range flip |
| Subtree `sum` | Range sum = sum field of the middle piece after split |

## Approach

**Root idea:** maintain in each node `sum = value + left.sum + right.sum`. Reverse does not change a subtree’s sum, so lazy reverse only swaps children; sums stay valid. A sum query splits out `[a..b]` and reads `Mid->sum`.

**Mental picture:** beads with numbers in a treap. Flipping a window shuffles order but the total weight of the window is unchanged — store that weight on the subtree root.

**How that becomes the method**

1. Treap nodes store `val`, `sum`, `sz`, `rev`.
2. `pull(v)`: recompute `sz` and `sum` from children + `val` (after push).
3. `t=1`: split `[a..b]`, toggle `rev` on mid, merge back.
4. `t=2`: split `[a..b]`, print `mid->sum`, merge back.

**Data structures:** implicit treap with `{val, sum, sz, rev, left, right}`; 64-bit sums.

## Walkthrough (on a sample)

Sum `[2..4]` on `2 1 3 4 5 3 4 4` → `1+3+4=8`. Reverse `[3..6]` → array becomes `2 1 3 5 4 3 4 4`…; sum `[2..4]` → `1+3+5=9`.

## Pitfalls

- Updating `sum` incorrectly when reversing (sum is invariant — do not rebuild from flipped order wrongly).  
- Forgetting `pull` after merge/split.  
- 32-bit sum overflow.

## Complexity

- Time: `O((n + m) log n)`  
- Space: `O(n)`

## Key takeaway

**Range reverse + range sum = implicit treap with lazy reverse and subtree sums.**
