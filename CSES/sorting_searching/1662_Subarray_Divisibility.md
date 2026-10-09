# [1662 — Subarray Divisibility](https://cses.fi/problemset/task/1662)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** prefix sums, modular arithmetic, counting  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to count the number of subarrays where the sum of values is divisible by `n`.

### Input

The first input line has an integer `n`: the size of the array.

The next line has `n` integers `a1, a2, ..., an`: the contents of the array.

### Output

Print one integer: the required number of subarrays.

### Constraints

- `1 <= n <= 2e5`
- `-1e9 <= ai <= 1e9`

### Examples

**Input**
```
5
3 1 2 7 4
```

**Output**
```
1
```

---

## What this problem really is

Count subarrays whose sum is `≡ 0 (mod n)`, where `n` is the **array length** (not a separate modulus input). Negatives allowed.

## What to use

| Tool | Why |
|------|-----|
| Prefix sums mod `n` | `sum[L..R] ≡ 0 mod n` iff `pref[R] ≡ pref[L-1] (mod n)` |
| Frequency of residues | Count pairs of prefixes with the same residue |
| Careful negative mod | In C++, `(-1) % n` can be negative — normalize to `[0, n)` |

## Approach

**Root idea:** two prefixes with the same remainder mod `n` bound a subarray divisible by `n`. Count, for each residue, how many prefixes have seen it; each new prefix with residue `r` pairs with all previous `r`’s.

**Mental picture:** only the remainder of the running sum matters; every time you revisit a remainder, every earlier visit closes a divisible segment.

**Data structures**

- Array or map `cnt[0..n-1]` — frequency of prefix residues.
- `cnt[0] = 1` initially (pref 0).
- `long long pref = 0`, `ans = 0`.

**Steps**

1. For each `a` in the array:
   - `pref += a`
   - `r = pref % n`; if `r < 0` then `r += n`
   - `ans += cnt[r]`
   - `cnt[r]++`
2. Print `ans`.

## Walkthrough (on a sample)

`n=5`, array `3 1 2 7 4`.

One subarray with sum divisible by 5: e.g. whole? `17` no. `1+2+7=10` — yes, one such → **1**.

Residues of prefixes collide once for that segment.

## Pitfalls

- Modulo by something other than `n` (misread statement).
- Leaving negative residues un-normalized.
- Forgetting `cnt[0] = 1` (subarrays from the start).
- `int` for `ans` — up to `n(n+1)/2`.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Subarray sum divisible by `n` = count matching prefix residues mod `n`** (normalize negatives).
