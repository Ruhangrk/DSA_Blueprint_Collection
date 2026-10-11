# [3157 — Collecting Numbers Distribution](https://cses.fi/problemset/task/3157)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** combinatorics, Eulerian numbers, permutations, DP  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array that contains each number between `1..n` exactly once. You collect the numbers in increasing order from `1` to `n`. On each round, you traverse the array from left to right and collect as many consecutive numbers as possible, starting from the smallest number that has not been collected yet.

Your task is to determine, for each `k=1,2,...,n`, the number of arrays that require exactly `k` rounds to collect all the numbers.

### Input

The only line has an integer `n`.

### Output

Print `n` numbers: for each `k=1,2,...,n`, the answer modulo `1e9+7`.

### Constraints

- `1 <= n <= 5000`

### Examples

**Input**
```
3
```

**Output**
```
1 4 1
```

Explanation: The arrays are `[1,2,3]` (1 round), `[1,3,2]` (2 rounds), `[2,1,3]` (2 rounds), `[2,3,1]` (2 rounds), `[3,1,2]` (2 rounds), and `[3,2,1]` (3 rounds).

---

## What this problem really is

Count permutations of `1..n` by how many “Collecting Numbers” rounds they need — equivalently, by how many consecutive-value position breaks they have.

## What to use

| Tool | Why |
|------|-----|
| Break statistic | Rounds = `1 +` #{ `i` : `pos[i+1] < pos[i]` } |
| Eulerian numbers | That break count is an Eulerian statistic on permutations |
| DP `A[n][t]` | Standard Eulerian recurrence in `O(n^2)` |

## Approach

**Root idea:** a new round starts exactly when the next needed value sits to the left of the previous one. So exactly `k` rounds means exactly `k-1` indices `i` with `pos[i+1] < pos[i]`.

**Mental picture:** walk values `1,2,...,n` in order and look at where they sit in the array. Each time the next value is “behind” you, you must rewind for another left-to-right pass.

**How that becomes the method**

1. Let `A[n][t]` = number of perms of `n` with exactly `t` breaks (`t` in `0..n-1`).
2. These are Eulerian numbers. Recurrence (one common form):  
   `A[i][j] = (j+1) * A[i-1][j] + (i-j) * A[i-1][j-1]`  
   with `A[0][0]=1` (or seed `A[1][0]=1`).
3. Answer for rounds `k` is `A[n][k-1]` mod `1e9+7`.
4. Print `A[n][0] .. A[n][n-1]`.

**Data structures:** 2D DP `O(n^2)` or two rolling rows; mod arithmetic.

## Walkthrough (on a sample)

`n=3` permutations and breaks `#{pos[i+1]<pos[i]}`:

| perm | breaks | rounds |
|------|--------|--------|
| `[1,2,3]` | 0 | 1 |
| `[1,3,2]` | 1 | 2 |
| `[2,1,3]` | 1 | 2 |
| `[2,3,1]` | 1 | 2 |
| `[3,1,2]` | 1 | 2 |
| `[3,2,1]` | 2 | 3 |

Counts: `1,4,1` — matches `A[3][0], A[3][1], A[3][2]`.

## Pitfalls

- Off-by-one: rounds `k` ↔ breaks `k-1` (do not print `A[n][k]`).
- Wrong Eulerian recurrence variant (ascent vs descent indexing) — verify on `n=3`.
- Output format: `n` numbers on one line (as sample) is fine; problem says “print `n` numbers”.

## Complexity

- Time: `O(n^2)`
- Space: `O(n)` with rolling arrays (or `O(n^2)`)

## Key takeaway

Collecting-Numbers rounds = `1 +` consecutive-value left-of breaks → Eulerian numbers via a simple DP.
