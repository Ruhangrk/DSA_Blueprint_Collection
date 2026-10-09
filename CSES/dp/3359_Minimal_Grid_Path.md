# [3359 — Minimal Grid Path](https://cses.fi/problemset/task/3359)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, greedy on grid, lexicographic  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an `n x n` grid whose each square contains a letter.

You should move from the upper-left square to the lower-right square. You can only move right or down.

What is the lexicographically minimal string you can construct?

### Input

The first line has an integer `n`: the size of the grid.

After this, there are `n` lines that describe the grid. Each line has `n` letters between `A` and `Z`.

### Output

Print the lexicographically minimal string.

### Constraints

- `1 <= n <= 3000`

### Examples

**Input**
```
4
AACA
BABC
ABDA
AACA
```

**Output**
```
AAABACA
```

---

## What this problem really is

Paths from `(0,0)` to `(n-1,n-1)` with only right/down. Concatenate letters on the visited cells (including start and end). Among all such strings (all length `2n-1`), pick the **lexicographically smallest**.

## What to use

| Tool | Why |
|------|-----|
| Frontier of cells | At step `k` you are always on cells with `r+c = k` |
| Greedy min letter | Lex-smallest string is built left-to-right: always append the smallest letter you can still reach with an optimal prefix |
| `visited` / set of positions | `n = 3000` → cannot store full best strings at every cell (`O(n^2)` strings would explode) |
| `O(n^2)` pass | Each cell enters the frontier at most once |

## Approach

**Root idea:** a lex-minimal string is decided **character by character**. After a fixed optimal prefix of length `k`, every continuation that can still produce a global optimum must sit on a certain set of cells (the frontier). The next character must be the **minimum** letter among all right/down neighbors of that frontier; then the new frontier is exactly those neighbor cells that carry that minimum letter.

**Mental picture:** wavefronts on diagonals `r + c = 0, 1, 2, …, 2n-2`. On each diagonal, among cells reachable via some still-optimal path, keep only those with the best next letter.

**Data structures**

- Grid `char g[n][n]`.
- Current frontier: list/set of `(r,c)` that end an optimal prefix.
- Answer string `ans` (reserve `2n-1`).
- Mark array so the same cell is not pushed twice in one step.

**Steps**

1. Start with frontier `{(0,0)}`, `ans = g[0][0]`.
2. Repeat until you reach `(n-1,n-1)`:
   - Collect all valid neighbors `(r, c+1)` and `(r+1, c)` of frontier cells.
   - Let `ch` = minimum letter among those neighbors.
   - Append `ch` to `ans`.
   - New frontier = all such neighbors whose letter equals `ch` (dedupe).
3. Print `ans`.

Every path has the same length, so “greedy letter by letter” with the reachable frontier is correct for lex order.

## Walkthrough (on a sample)

`n = 4`, grid:

```
A A C A
B A B C
A B D A
A A C A
```

- Start `A` at `(0,0)`.
- Next candidates from start: right `A`, down `B` → take **`A`**, frontier → `{(0,1)}`.
- From `(0,1)`: right `C`, down `A` → take **`A`**, frontier → `{(1,1)}`.
- Continue this way; one optimal string is **`AAABACA`** (length `7 = 2*4-1`).

Whenever several cells share the min letter, keep **all** of them in the frontier — a later diagonal may need a different branch.

## Pitfalls

- **Storing full `dp[r][c]` strings** — memory/time death at `n = 3000`.
- **Dropping tied frontier cells** — must keep every cell that matches the min letter.
- **Only moving right/down** — no left/up.
- **Including start letter** — string length is `2n-1`, not `2n-2`.

## Complexity

- Time: `O(n^2)`  
- Space: `O(n^2)` for the grid / marks, `O(n)` for a frontier

## Key takeaway

**Lex-best path on a DAG:** grow the answer one character at a time; keep the frontier of positions that still match the best prefix, then always append the minimum possible next letter.
