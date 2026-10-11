# [2215 — Monotone Subsequences](https://cses.fi/problemset/task/2215)

**Platform:** CSES  
**Source:** CSES Problem Set — Construction Problems  
**Tags:** construction, Erdős–Szekeres, permutation  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to create a permutation of numbers `1,2,...,n` whose longest monotone subsequence has exactly `k` elements.

A monotone subsequence is either increasing or decreasing. For example, some monotone subsequences in `[2,1,4,5,3]` are `[2,4,5]` and `[4,3]`.

### Input

The first input line has an integer `t`: the number of tests.

After this, there are `t` lines. Each line has two integers `n` and `k`.

### Output

For each test, print a line that contains the permutation. You can print any valid solution. If there are no solutions, print `IMPOSSIBLE`.

### Constraints

- `1 <= t <= 1000`
- `1 <= k <= n <= 100`

### Examples

**Input**
```
3
5 3
5 2
7 7
```

**Output**
```
2 1 4 5 3
IMPOSSIBLE
1 2 3 4 5 6 7
```

---

## What this problem really is

Build a perm whose `max(LIS length, LDS length) = k` exactly (longest monotone = longest inc or dec).

## What to use

| Tool | Why |
|------|-----|
| Erdős–Szekeres | Any perm of `> (a-1)(b-1)` has LIS `>=a` or LDS `>=b` |
| Layered decreasing blocks | Control LIS/LDS |
| Feasibility | Impossible when `k` too small for `n` |

## Approach

**Root idea:** you need both LIS `<= k` and LDS `<= k`, and at least one equal to `k`. Concatenate decreasing runs of length `k` (each run is decreasing ⇒ LDS at least run length; across runs increasing ⇒ LIS grows with #runs).

**Mental picture:** write numbers in batches of size `k`, each batch written downward, batches in increasing ranges — like a zigzag that caps both ascent and descent length near `k`.

**How that becomes the method**

1. If `k == n`: print `1 2 ... n` (or fully reversed) — monotone length `n`.  
2. If `k == 1`: only `n=1`, else impossible (for `n>=2` both directions exist? Actually single element — for `n>1`, any two comparable in order give mono length ≥2). So `k=1` only if `n=1`.  
3. Feasibility: you need `n <= k*k` by ES (else forced mono `>k`). Also `k >= 1`. More precisely: if `n > k*k`, impossible. Sample `5 2`: `5 > 4` → `IMPOSSIBLE`.  
4. Construction when `n <= k*k`: split `1..n` into chunks of size at most `k`, write each chunk in decreasing order, chunks covering consecutive ranges in increasing order. Then LIS `<=` #chunks (≤k) and LDS `<= k` (chunk size). Tune so max hits exactly `k` (ensure one chunk of size `k` or `k` chunks).

**Data structures:** just output vector size `n`.

## Walkthrough (on a sample)

`5 3`: e.g. `2 1 | 4 5 3` pattern with mono length 3. `5 2` impossible. `7 7` sorted.

## Pitfalls

- Forgetting Erdős–Szekeres bound → wrong possible answers.  
- Construction with LIS or LDS accidentally `> k`.  
- `k=n` / `k=1` edge cases.

## Complexity

- Time: `O(n)` per test  
- Space: `O(n)`

## Key takeaway

**Monotone length exactly `k` = decreasing blocks of size `k` (with `n <= k^2`)**, else `IMPOSSIBLE`.
