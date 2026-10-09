# [1070 — Permutations](https://cses.fi/problemset/task/1070)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** construction, greedy  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A permutation of integers `1, 2, ..., n` is called beautiful if there are no adjacent elements whose difference is `1`.

Given `n`, construct a beautiful permutation if such a permutation exists.

### Input

The only input line contains an integer `n`.

### Output

Print a beautiful permutation of integers `1, 2, ..., n`. If there are several solutions, you may print any of them. If there are no solutions, print `"NO SOLUTION"`.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Example 1**

**Input**
```
5
```

**Output**
```
4 2 5 3 1
```

**Example 2**

**Input**
```
3
```

**Output**
```
NO SOLUTION
```

---

## What this problem really is

Build a permutation of `1..n` with no two consecutive numbers placed next to each other (absolute difference ≠ 1 for adjacent positions). Or prove impossible.

## What to use

| Tool | Why |
|------|-----|
| Case analysis on `n` | Small `n` fail; large `n` have a pattern |
| Evens then odds | Separates consecutive integers |

## Approach

**Root idea:** put all even numbers first (ascending), then all odds (ascending). Consecutive integers have opposite parity, so they never sit inside the even block or inside the odd block. The only junction is between the last even and the first odd — check that gap isn’t 1.

**Mental picture:** two piles — even stepping stones, then odd — consecutives live in different piles.

**When impossible**

- `n = 2` or `n = 3`: every permutation has a consecutive pair → `"NO SOLUTION"`.
- `n = 1`: just `1` (no adjacent pair) — OK.

**Steps**

1. If `n == 2` or `n == 3`: print `NO SOLUTION`.
2. Else print all evens `2,4,...,` then all odds `1,3,5,...`.

For `n=4`: `2 4 1 3` — junction `|4-1|=3 ≠ 1`. For `n=5`: `2 4 1 3 5` also works (sample shows another valid order).

## Walkthrough (on a sample)

`n=5`: `2 4 1 3 5` (or sample `4 2 5 3 1`) — no adjacent differ by 1.

`n=3`: impossible → **NO SOLUTION**.

## Pitfalls

- Claiming impossible for all small `n` including `1`.
- Odds then evens without checking the junction (`1` next to `2` at the boundary for some orders).
- `O(n^2)` constructions — need `O(n)` output for `n=1e6`.

## Complexity

- Time: `O(n)`  
- Space: `O(1)` extra

## Key takeaway

**Beautiful permutation = evens then odds** (impossible only for `n = 2, 3`).
