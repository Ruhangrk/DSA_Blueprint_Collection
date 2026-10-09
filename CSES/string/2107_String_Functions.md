# [2107 — String Functions](https://cses.fi/problemset/task/2107)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** Z-algorithm, KMP prefix function  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

We consider a string of `n` characters, indexed `1, 2, ..., n`. Your task is to calculate all values of the following functions:

- `z(i)` denotes the maximum length of a substring that begins at position `i` and is a prefix of the string. In addition, `z(1) = 0`.
- `pi(i)` denotes the maximum length of a substring that ends at position `i`, is a prefix of the string, and whose length is at most `i-1`.

Note that the function `z` is used in the Z-algorithm, and the function `pi` is used in the KMP algorithm.

### Input

The only input line has a string of length `n`. Each character is between `a`–`z`.

### Output

Print two lines: first the values of the `z` function, and then the values of the `pi` function.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
abaabca
```

**Output**
```
0 0 1 2 0 0 1
0 0 1 1 2 0 1
```

---

## What this problem really is

Compute the classic **Z-array** and **KMP prefix function** (`pi` / LPS) of one string. Definitions are 1-indexed in the statement; `z(1)` is forced to `0` (usual Z-algorithm often sets `Z[0] = 0` or `n` — here follow the statement).

## What to use

| Tool | Why |
|------|-----|
| Z-algorithm | Linear computation of all `z(i)` using a maintained `[L,R]` match window |
| KMP prefix function | Linear computation of all `pi(i)` via failure links |
| 0-based arrays carefully mapped to 1-based output | Avoid off-by-ones |

## Approach

**Root idea (Z):** `z(i)` = longest common prefix of `s` and `s[i..]`. When `i` lies inside a previous Z-box `[L,R]`, copy from the mirror `z(i-L+1)` (capped by `R-i+1`), then try to extend.

**Root idea (pi):** `pi(i)` = longest proper border of the prefix `s[1..i]`. Grow a candidate length `len = pi(i-1)`; while mismatch, fall back `len = pi(len)`; on match, `len++`.

**Mental picture:** Z looks forward from each start (“how much prefix do I still see?”). `pi` looks backward from each end (“how much am I closing a prefix?”).

**Steps**

1. Run Z-algorithm; set `z[1] = 0`; print `z[1..n]`.
2. Run KMP prefix on the same string; print `pi[1..n]` (`pi[1] = 0`).

## Walkthrough (on a sample)

`abaabca`:

- Z: position 4 starts `ab...` matching prefix `ab` → `z(4)=2`, etc. → `0 0 1 2 0 0 1`.
- `pi`: prefix `abaab` has border `ab` of length 2 at position 5, etc. → `0 0 1 1 2 0 1`.

## Pitfalls

- Setting `z(1) = n` (common in some Z codes) — here must be `0`.
- Mixing 0-based loops with 1-based printed indices.
- `O(n^2)` naive LCP from every position — TLE.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Z = forward prefix matches from each index; `pi` = longest proper border of each prefix** — both in linear time.
