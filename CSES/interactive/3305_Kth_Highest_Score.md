# [3305 — K-th Highest Score](https://cses.fi/problemset/task/3305)

**Platform:** CSES  
**Source:** CSES Problem Set — Interactive Problems  
**Tags:** interactive, binary search, selection  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There were `n` coders from Finland and `n` coders from Sweden in a programming contest. It turned out that after the contest, each coder had a distinct score.

Your task is to find the `k`-th highest score in the contest.

To do this, you can ask questions: you can choose a country (Finland or Sweden) and an integer `i` and you will be told the `i`-th highest score for the chosen country.

### Interaction

This is an interactive problem. Your code will interact with the grader using standard input and output. You should start by reading two integers `n` and `k`.

On your turn, you can print one of the following:

- `F i`, where `1 <= i <= n`: ask the `i`-th highest score for Finland.
- `S i`, where `1 <= i <= n`: ask the `i`-th highest score for Sweden.
- `! s`: report that the `k`-th highest score is `s`. Terminate after this.

Flush after every print.

### Constraints

- `1 <= n <= 1e5`
- `1 <= k <= 2n`
- each score is between `1` and `1e9`
- you can ask at most `100` queries of the first two types in total

### Examples

```
3 1
F 1
9
S 1
8
! 9
```

Explanation: Finland `[9,4,3]`, Sweden `[8,6,1]`; `k=1` → overall highest is `9`.

---

## What this problem really is

Two sorted descending arrays `F[1..n]`, `S[1..n]` (accessible only by index). Find the `k`-th largest among all `2n` distinct values, with ≤100 probes.

## What to use

| Tool | Why |
|------|-----|
| Binary search on ranks | Selection in two sorted arrays |
| Compare `F[mid]` vs `S[...]` | Drop half of candidates like median-of-medians lite |
| ≤`~2 log n` style loops | Fits in 100 queries |

## Approach

**Root idea:** classic “k-th element of two sorted arrays.” Binary-search how many of the top-`k` come from Finland: if you take `i` from Finland (the `i` highest), you need `k-i` from Sweden; check consistency with cross comparisons `F[i]` vs `S[k-i]` / `S[k-i+1]`.

**Mental picture:** two descending ladders. You want the `k`-th rung when both ladders are merged. Guess how many rungs come from Finland’s ladder and verify with a couple of peeks.

**How that becomes the method**

1. Handle edge cases (`k=1` → max of `F[1],S[1]`, etc.).
2. Binary search `i` = number taken from Finland in the top `k` (`max(0, k-n) .. min(k,n)`).
3. With a few `F`/`S` queries around the boundary, decide whether to raise/lower `i`.
4. When the cut is correct, the answer is `max(F[i], S[k-i])` (with indices only if positive; treat missing side as `-inf`).

Alternate: binary search the score value using “how many scores `> mid`” by binary searching each country’s arrays — also `O(log^2)` queries.

**Data structures:** none; memoize queried cells to save budget.

## Walkthrough (on a sample)

`k=1`: query both first places, pick `9`.

## Pitfalls

- 1-based “i-th highest” indexing.  
- Burning queries by not caching.  
- Off-by-one in the two-array selection cut.

## Complexity

- Queries: `O(log n)` (well under 100)  
- Space: `O(1)` or small cache

## Key takeaway

**k-th among two sorted lists with few probes = binary-search the split** (two-array selection).
