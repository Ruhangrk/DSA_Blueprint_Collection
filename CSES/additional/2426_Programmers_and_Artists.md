# [2426 — Programmers and Artists](https://cses.fi/problemset/task/2426)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Greedy exchange argument, sorting, prefix/suffix top-k with heaps  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A company wants to hire `a` programmers and `b` artists from `n` applicants. Each applicant has a programming skill `x` and an artistic skill `y`. A programmer contributes `x`, an artist contributes `y`, and each applicant can be hired in at most one role. Select the hires to maximize the total skill.

### Input

- The first line has `a`, `b`, and `n`.
- The next `n` lines each have `x` and `y`.

### Output

Print the maximum total skill.

### Constraints

- `1 <= n <= 2e5`
- `0 <= a, b <= n`, `a + b <= n`
- `1 <= x, y <= 1e9`

### Examples

**Input**
```
2 1 4
3 7
9 8
1 5
4 2
```

**Output**
```
20
```

**Explanation:** Hire programmers with skills `9` and `4`, and artist with skill `7`.

---

## What this problem really is

We pick `a` applicants to be programmers and `b` to be artists. Each applicant is worth either `x` or `y` depending on their role, and some applicants stay unhired. The choice of who gets which role is a matching-like problem, but a simple ordering makes it easy.

## What to use

| Tool | Why |
|------|-----|
| Sort by `d = x - y`, largest first | Programmers should be the applicants who gain most from being programmers |
| Exchange argument | Shows that in an optimal answer, every programmer comes before every artist in this order |
| Prefix top-`a` sums of `x` (min-heap) | Best `a` programmers among a prefix |
| Suffix top-`b` sums of `y` (min-heap) | Best `b` artists among the rest |

## Approach

**Root idea:** if a programmer `j` has a smaller `d` than an artist `k`, swapping their roles changes the total by `d_k - d_j`, which is at least `0`. So there is always an optimal solution where all programmers come before all artists when sorted by `d` descending. That turns the problem into: choose a split point, pick the best `a` programmers before it and the best `b` artists after it.

**Mental picture:** line up the applicants by how much better they are as programmers than as artists, best programmer-leaning first. Draw a cut line. Everyone to the left is a candidate programmer, everyone to the right is a candidate artist. The cut line that gives the largest total wins.

**How that becomes the method**

1. Sort applicants by `d = x - y` descending.
2. `pre[i]` = the largest sum of `a` values of `x` among the first `i` applicants (valid when `i >= a`). Compute with a min-heap that keeps the `a` largest `x` seen so far.
3. `suf[i]` = the largest sum of `b` values of `y` among applicants `i..n-1` (valid when `n - i >= b`). Compute from the right with a min-heap keeping the `b` largest `y`.
4. Answer = maximum over `i` of `pre[i] + suf[i]`, where the first `i` applicants are the programmer candidates and the rest are artist candidates.

**Data structures:** an array of applicants, two arrays of prefix/suffix sums, and two min-heaps.

## Walkthrough (on a sample)

`a = 2`, `b = 1`, `n = 4`. Applicants (x, y): `(3,7)`, `(9,8)`, `(1,5)`, `(4,2)`.

Differences `d = x - y`: `-4, 1, -4, 2`.

Sort by `d` descending: `(4,2)` with `d=2`, `(9,8)` with `d=1`, `(3,7)` with `d=-4`, `(1,5)` with `d=-4`.

Prefix top-2 sums of `x` (only valid from prefix length 2):
- First 2 items: `x = 4, 9`. Sum `13`.
- First 3 items: `x = 4, 9, 3`. Top 2 sum `13`.

Suffix top-1 sums of `y` (for suffix starting at index `i`):
- From index `2`: items `(3,7)`, `(1,5)`. Top 1 of `y`: `7`.
- From index `3`: item `(1,5)`. Top 1: `5`.

Candidate splits:
- Split after 2 items (prefix length 2, suffix starts at 2): `13 + 7 = 20`.
- Split after 3 items (suffix starts at 3): `13 + 5 = 18`.

Maximum `20`. Matches. The best choice is programmers `9` and `4`, artist `7`.

## Pitfalls

- Choosing the top-`a` by `x` globally, then top-`b` by `y` from the rest. This ignores the trade-off that an applicant with big `x` might be more valuable as an artist.
- Sorting by `x` or `y` alone. The correct key is `x - y`.
- Forgetting the feasibility bounds: the prefix needs at least `a` elements and the suffix at least `b`.
- Overflow. Sums reach about `2e5 * 1e9 = 2e14`, so use 64-bit integers.
- Mishandling ties. Equal `x - y` values can be ordered arbitrarily; the exchange argument still holds.

## Complexity

- Time: `O(n log n)`.
- Space: `O(n)`.

## Key takeaway

When two roles compete for people with different gains, sort by the difference of gains. Then the optimal assignment is a single split point, and prefix/suffix top-k sums with heaps find the best split.
