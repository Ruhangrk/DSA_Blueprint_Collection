# [2421 — Counting Reorders](https://cses.fi/problemset/task/2421)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** inclusion-exclusion, combinatorics, DP  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  
**Editorial:** [RBTREE — Counting Reorders](https://rbtr.ee/cses-2421)

---

## Problem Statement

Calculate the number of ways you can reorder the characters of a string so that no two adjacent characters are the same.

For example, the answer for `aabc` is `6`, because the possible orders are `abac`, `abca`, `acab`, `acba`, `baca`, and `caba`.

### Input

The only input line has a string that consists of `n` characters between `a`–`z`.

### Output

Print an integer: the answer modulo `1e9+7`.

### Constraints

- `1 <= n <= 5000`

### Examples

**Input**
```
aabc
```

**Output**
```
6
```

---

## What this problem really is

Count distinct rearrangements of a multiset of letters with no two equal letters adjacent.

## What to use

| Tool | Why |
|------|-----|
| Inclusion-exclusion on “glue” | Force some adjacent pairs equal, then cancel bad cases |
| Block DP by letter type | Build rearrangements as sequences of same-letter blocks |
| Binomials | Split a letter’s count into `k` nonempty blocks and insert them among existing blocks |

## Approach

**Root idea:** instead of fighting “no equal neighbors” directly, count rearrangements by how they split into same-letter *blocks*, then use IE so only fully split (no forced equal glue beyond real runs) configurations that match the “no adjacent equals” condition survive.

**Mental picture:** take all copies of letter `a`, chop them into `k` nonempty runs, and weave those runs into the existing block sequence of other letters. Doing this for every letter builds every string as a sequence of monochrome blocks.

**How that becomes the method**

1. Count frequencies `cnt[c]` for letters that appear.
2. Let `f[j]` = ways (overcount with IE structure) to arrange the processed letters into exactly `j` blocks. Start `f = [1]` (zero letters → one empty arrangement with `0` blocks).
3. When adding a letter with `c = cnt`, for each old `j` and each `k = 1..c`:
   - ways to split `c` identical copies into `k` nonempty parts: `C(c-1, k-1)`
   - ways to place those `k` new blocks among a sequence that will have `j+k` blocks: `C(j+k, k)`
   - add `f[j] * C(c-1,k-1) * C(j+k,k)` into `g[j+k]`
4. After all letters, IE combine:  
   `ans = sum_i (-1)^{n-i} * f[i]`  
   (equivalently: add `f[i]` when `i%2 == n%2`, else subtract).  
   This cancels rearrangements that still have illegal equal-adjacencies in the IE sense.
5. Reduce mod `1e9+7`.

**Data structures:** frequency array size `26`; rolling `f` vector length `<= n+1`; factorial / binomial table up to `n`.

**Complexity note:** naive looks like `O(26 n^2)`, but sum of `c` over letters is `n`, so total is `O(n^2)`.

## Walkthrough (on a sample)

`aabc` → counts `a:2, b:1, c:1`, `n=4`.

Process letters one type at a time (order of types does not change the final IE answer):

- After placing both `a`s: can form `1` block `aa` or `2` blocks `a|a` (with the binomial splits).
- Insert `b` as `1` block into the gaps/slots among existing blocks.
- Insert `c` similarly.

Final signed combination of `f[i]` yields `6`, matching the listed strings — each has no equal neighbors; strings like `aabc` itself are excluded.

## Pitfalls

- Forgetting IE at the end and printing `f[n]` or a plain multinomial.
- Using `C(j+k, j)` vs `C(j+k, k)` inconsistently (same value, but keep one convention).
- `k` must be at least `1` and at most `c`; empty letter types skipped.
- Modular negatives when subtracting.

## Complexity

- Time: `O(n^2)`
- Space: `O(n)` for DP + binomials

## Key takeaway

“No two equal adjacent” on a multiset → build by monochrome blocks, then inclusion-exclusion on the block count.
