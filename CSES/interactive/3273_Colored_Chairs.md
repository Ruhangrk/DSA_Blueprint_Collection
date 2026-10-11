# [3273 — Colored Chairs](https://cses.fi/problemset/task/3273)

**Platform:** CSES  
**Source:** CSES Problem Set — Interactive Problems  
**Tags:** interactive, binary search, pigeonhole  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` chairs arranged in a circle. Each chair is either red or blue. The chairs are numbered `1, 2, ..., n`; chairs `i` and `i+1` are next to each other for all `1 <= i <= n`. Here chair `n+1` refers to chair `1`.

Your task is to find two chairs that have the same color and are next to each other.

To do this, you can ask questions: you can choose a chair and you will be told the color of that chair.

### Interaction

Read a single integer `n` first.

Print one of:

- `? i` (`1 <= i <= n`): grader returns `R` or `B`
- `! i`: claim that chairs `i` and `i+1` (wrap for `n`) have the same color; then exit

Flush every line.

### Constraints

- `3 <= n <= 2e5`, `n` is odd
- at most `20` questions of type `?`

### Examples

```
5
? 1
R
? 2
B
? 3
B
! 2
```

---

## What this problem really is

Find an adjacent monochromatic pair on a circle of odd length, with ≤20 color queries.

## What to use

| Tool | Why |
|------|-----|
| Pigeonhole on odd cycle | Cannot alternate R/B all the way around an odd cycle — a same-color edge must exist |
| Binary search on an arc | Shrink a segment whose endpoints differ until neighbors match |
| Few queries | `O(log n) <= 20` for `n<=2e5` |

## Approach

**Root idea:** because `n` is odd, a perfect 2-coloring without adjacent equals is impossible. Query chair `1` and walk/binary-search to locate a place where the color pattern must “break.”

**Mental picture:** beads on an odd necklace, two colors. Somewhere two neighbors match. Compare colors at the ends of a shrinking arc; when the arc is length 1, those two chairs are adjacent and… if ends differ you keep cutting; actually the standard trick:

1. Query position `1` and position `1 + (n-1)/2` (opposite-ish).  
2. Maintain an interval along the circle where the two endpoints have **different** colors (such an interval of odd length always contains a same-color adjacent pair — because an odd-length path with different ends cannot be strict alternating all the way).  
3. Probe the midpoint; keep the half that still has different-colored endpoints (or report if you see an adjacent equal while querying neighbors).  
4. When the interval shrinks to two adjacent chairs with the same color, output `!` on the left index. If you ever query two neighbors equal, answer immediately.

More concretely: start with chairs `1` and `2` after checking — if equal, done. Else binary search is on finding where the run breaks relative to chair `1`’s color around the circle.

**Data structures:** none; store a few queried colors in a map/cache.

## Walkthrough (on a sample)

`1=R`, `2=B`, `3=B` → chairs `2` and `3` same → `! 2`.

## Pitfalls

- Ignoring the wrap-around edge `(n,1)`.  
- Even `n` logic (guaranteed odd).  
- Not flushing / >20 queries.

## Complexity

- Queries: `O(log n)`  
- Space: `O(1)`

## Key takeaway

**Odd circular 2-coloring must have a monochrome edge** — binary-search an arc with disagreeing ends until you pin it down.
