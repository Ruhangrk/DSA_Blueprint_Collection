# [3112 — Hidden Integer](https://cses.fi/problemset/task/3112)

**Platform:** CSES  
**Source:** CSES Problem Set — Interactive Problems  
**Tags:** interactive, binary search  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a hidden integer `x`. Your task is to find the value of `x`.

To do this, you can ask questions: you can choose an integer `y` and you will be told if `y < x`.

### Interaction

This is an interactive problem. Your code will interact with the grader using standard input and output. You can start asking questions right away.

On your turn, you can print one of the following:

- `? y`, where `1 <= y <= 1e9`: ask if `y < x`. The grader will return `YES` if `y < x` and `NO` otherwise.
- `! x`: report that the hidden integer is `x`. Your program must terminate after this.

Each line should be followed by a line break. You must make sure the output gets flushed after printing each line.

### Constraints

- `1 <= x <= 1e9`
- you can ask at most `30` questions of type `?`

### Examples

```
? 3
YES
? 6
YES
? 7
NO
! 7
```

---

## What this problem really is

Find unknown `x` in `{1..1e9}` with predicates “is `y < x`?”, ≤30 queries — binary search.

## What to use

| Tool | Why |
|------|-----|
| Binary search on value | Each query halves the range |
| Flush (`endl` / `fflush`) | Required for interactive I/O |
| Careful bounds | `YES` ⇒ `x >= y+1`; `NO` ⇒ `x <= y` |

## Approach

**Root idea:** maintain a possible interval `[lo, hi]` for `x`. Query mid; shrink until one value remains.

**Mental picture:** guess a cut on the number line; the oracle says whether the secret is strictly above the cut.

**How that becomes the method**

1. `lo = 1`, `hi = 1e9`.
2. While `lo < hi`: pick `y = lo + (hi-lo)/2` (or similar). Ask `? y`.
   - `YES` (`y < x`) → `lo = y+1`
   - `NO` → `hi = y`
3. Print `! lo`.

**Data structures:** none beyond bounds.

## Walkthrough (on a sample)

Narrows past `3` and `6` still `YES`, then `7` is not `< x` so `x = 7`.

## Pitfalls

- Forgetting to flush.  
- Off-by-one when updating `lo`/`hi`.  
- Linear search (too many queries).

## Complexity

- Queries: `O(log 1e9) <= 30`  
- Space: `O(1)`

## Key takeaway

**Hidden integer with `<` comparisons = binary search** plus interactive flush discipline.
