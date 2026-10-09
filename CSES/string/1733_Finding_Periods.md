# [1733 — Finding Periods](https://cses.fi/problemset/task/1733)

**Platform:** CSES  
**Source:** CSES Problem Set — String Algorithms  
**Tags:** Z-algorithm, periods, string periodicity  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A period of a string is a prefix that can be used to generate the whole string by repeating the prefix. The last repetition may be partial. For example, the periods of `abcabca` are `abc`, `abcabc` and `abcabca`.

Your task is to find all period lengths of a string.

### Input

The only input line has a string of length `n` consisting of characters `a`–`z`.

### Output

Print all period lengths in increasing order.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
abcabca
```

**Output**
```
3 6 7
```

---

## What this problem really is

Find every length `k` (`1 <= k <= n`) such that `s[i] = s[i % k]` for all `i` (equivalently: the string is the prefix of length `k` tiled, with a possible short last tile). Length `n` is always a period.

## What to use

| Tool | Why |
|------|-----|
| Z-array | `Z[i]` = longest match of `s[i..]` with the prefix of `s` |
| Criterion `Z[k] >= n-k` | Suffix from `k` equals the needed prefix fragment |
| Always include `n` | Whole string is a trivial period |

## Approach

**Root idea:** tiling with a block of length `k` means every character aligns with the character `k` positions earlier (and so on back to the prefix). That is exactly: the suffix starting at index `k` must equal the prefix of length `n-k`.

**Mental picture:** lay the prefix of length `k` end-to-end under the string; every column must match. The last block may hang off shorter.

**Steps**

1. Compute `Z[0..n-1]` of `s` (`Z[0]` unused / `n`).
2. For `k = 1 .. n-1`: if `Z[k] >= n-k`, then `k` is a period length.
3. Always output `n` as well.
4. Print in increasing order (the loop already is).

Equivalent hash check: for each `k`, verify all blocks `s[0..k)` vs `s[j..j+k)` for `j = k, 2k, ...` in `O(1)` per block → `O(n log n)` total by harmonic sum.

## Walkthrough (on a sample)

`s = abcabca` (`n = 7`).

- `k = 3`: suffix `abca` matches prefix `abca` → `Z[3] >= 4` → period.  
- `k = 6`: suffix `a` matches `a` → period.  
- `k = 7`: whole string → period.  
Output `3 6 7`.

## Pitfalls

- Confusing **border** lengths with **period** lengths (related but not the same list).
- Forgetting to include `n`.
- Checking only exact full repeats (`n % k == 0`) — partial last tile is allowed.
- `O(n^2)` character compares — TLE.

## Complexity

- Time: `O(n)` with Z-array  
- Space: `O(n)`

## Key takeaway

**Period length `k` iff `Z[k] >= n-k` (plus `k = n`)** — tile the prefix, last copy may be short.
