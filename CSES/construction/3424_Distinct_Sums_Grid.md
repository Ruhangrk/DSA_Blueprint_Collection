# [3424 — Distinct Sums Grid](https://cses.fi/problemset/task/3424)

**Platform:** CSES  
**Source:** CSES Problem Set — Construction Problems  
**Tags:** construction, grid, latin-like  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Create an `n x n` grid that fulfills the following requirements:

1. Each integer `1 ... n` appears `n` times in the grid.
2. If we create a set that consists of all sums in rows and columns, there are `2n` distinct values.

### Input

The only line has an integer `n`.

### Output

Print a grid that fulfills the requirements. You can print any valid solution. If there are no solutions, print `IMPOSSIBLE`.

### Constraints

- `1 <= n <= 1000`

### Examples

**Input**
```
5
```

**Output**
```
2 3 1 1 1
1 5 5 3 3
2 3 5 2 4
5 4 5 4 1
2 3 4 4 2
```

Explanation: Each of `1..5` appears five times; the `10` row/column sums are all distinct.

---

## What this problem really is

Fill an `n x n` matrix using each label `1..n` exactly `n` times so that the `n` row sums and `n` column sums are `2n` different numbers.

## What to use

| Tool | Why |
|------|-----|
| Sum range | Row/col sums lie in `[n, n^2]`; need `2n` distinct → tight packing |
| Structured fill | Diagonal blocks / cyclic shifts to separate row vs col sum sets |
| Impossibility | Small `n` may fail (check `n=1`: one sum used twice as row and col → only 1 distinct, need `2` → `IMPOSSIBLE` for `n=1`) |

## Approach

**Root idea:** force row sums into one numeric interval and column sums into a disjoint interval (or otherwise pairwise unique). With each value used `n` times, total sum of all entries is `n*(1+...+n)/2 * something` — total = `n * n(n+1)/2`. Sum of row sums equals sum of col sums equals that total.

**Mental picture:** budget chips `1..n` with `n` copies each; arrange so every row’s chip-total is unique and every column’s too, and no row total equals a column total.

**How that becomes the method**

1. `n=1`: `IMPOSSIBLE`.  
2. For larger `n`, use a known pattern (e.g. fill most cells with a base pattern, then adjust a few entries to split colliding sums). One family of constructions: put value `i` on a band / use latin rectangle fragments so row `r` sums to `A+r` and column `c` sums to `B+c` with `[A+1..A+n]` disjoint from `[B+1..B+n]`.  
3. Verify counts of each number = `n` after construction; tweak if needed.

(Exact published construction varies; the invariant to preserve is frequency + `2n` distinct line sums.)

**Data structures:** `n x n` int grid.

## Walkthrough (on a sample)

A valid `5x5` uses each of `1..5` five times and yields ten distinct line sums.

## Pitfalls

- Row sum equal to some column sum (only `2n-1` distinct).  
- Wrong frequencies.  
- Claiming possible for `n=1`.

## Complexity

- Time: `O(n^2)`  
- Space: `O(n^2)`

## Key takeaway

**Distinct row/col sums with balanced frequencies = separate the row-sum set from the column-sum set** by a structured fill (impossible for `n=1`).
