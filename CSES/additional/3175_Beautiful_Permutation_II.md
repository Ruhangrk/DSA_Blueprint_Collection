# [3175 — Beautiful Permutation II](https://cses.fi/problemset/task/3175)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** construction, permutations, greedy  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A permutation of integers `1,2,...,n` is called beautiful if there are no adjacent elements whose difference is `1`.

Given `n`, construct the lexicographically minimal beautiful permutation if such a permutation exists.

### Input

The only line contains an integer `n`.

### Output

Print the lexicographically minimal beautiful permutation of integers `1,2,...,n`. If there is no such permutation, print `"NO SOLUTION"`.

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
1 3 5 2 4
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

Build the lex-smallest permutation of `1..n` with no two consecutive values adjacent in position — or report impossible.

## What to use

| Tool | Why |
|------|-----|
| Case split on `n` | `n=2,3` impossible; `n=1` trivial; `n=4` special; `n>=5` odds-then-evens |
| O(n) print | `n` up to `1e6` |

## Approach

**Root idea:** try to start with `1` (lex-smallest). Listing all odds then all evens almost always works; the only small even size where the odd→even junction `|lastOdd - 2| = 1` is `n=4` (`3` next to `2`).

**Mental picture:** put all odd “stepping stones” first so the sequence climbs by 2, then jump to `2` and climb evens by 2. The dangerous jump is only `3→2` when `n=4`.

**How that becomes the method**

1. If `n == 1`: print `1`.
2. If `n == 2` or `n == 3`: print `NO SOLUTION`.
3. If `n == 4`: print `2 4 1 3` (lex-smallest possible; cannot start with `1`).
4. If `n >= 5`: print all odds `1 3 5 ...` then all evens `2 4 6 ...`.  
   Junction between last odd and `2` has difference ≥ `2`.

**Data structures:** none beyond output loop.

## Walkthrough (on a sample)

`n=5`: `1 3 5 2 4` — adjacent diffs `2,2,3,2`, none equal to `1`. Lex-smallest among beautiful perms.

`n=3`: any perm of `1,2,3` has a consecutive pair → `NO SOLUTION`.

## Pitfalls

- Using the introductory “evens then odds” construction (valid but not lex-smallest).
- Applying odds-then-evens to `n=4` → `1 3 2 4` invalid.
- `n=1` must not print `NO SOLUTION`.

## Complexity

- Time: `O(n)`
- Space: `O(1)` extra

## Key takeaway

Lex-smallest beautiful perm: odds then evens for `n>=5`, special `2 4 1 3` for `n=4`, impossible for `n=2,3`.
