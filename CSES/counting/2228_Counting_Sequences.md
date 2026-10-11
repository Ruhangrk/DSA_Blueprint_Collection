# [2228 — Counting Sequences](https://cses.fi/problemset/task/2228)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** inclusion-exclusion, surjections, combinatorics  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to count the number of sequences of length `n` where each element is an integer between `1..k` and each integer between `1..k` appears at least once in the sequence.

For example, when `n=6` and `k=4`, some valid sequences are `[1,3,1,4,3,2]` and `[2,2,1,3,4,2]`.

### Input

The only input line has two integers `n` and `k`.

### Output

Print one integer: the number of sequences modulo `1e9+7`.

### Constraints

- `1 <= k <= n <= 1e6`

### Examples

**Input**
```
6 4
```

**Output**
```
1560
```

---

## What this problem really is

Count surjective functions from `n` positions onto `{1..k}` — every value appears at least once.

## What to use

| Tool | Why |
|------|-----|
| Inclusion-exclusion | Total `k^n` minus those missing some values |
| Factorials / inv factorials | Fast `C(k,i)` for all `i` when `k` is up to `1e6` |
| Modular pow | Need `(k-i)^n` mod `1e9+7` |

## Approach

**Root idea:** start from all sequences (`k` choices per position), then subtract those that miss at least one required value, add back those that miss at least two, and so on.

**Mental picture:** `n` slots, each painted with one of `k` colors. You want every color used. IE flips the “forbidden missing set” size with alternating signs.

**How that becomes the method**

Number of surjections:

`sum_{i=0..k} (-1)^i * C(k,i) * (k-i)^n`

- Choose `i` values to forbid entirely: `C(k,i)` ways.
- Each position picks from the remaining `k-i` values: `(k-i)^n`.
- Sign `(-1)^i`.

Same as `k! * S(n,k)` where `S` is a Stirling number of the second kind, but IE is what scales to `n,k <= 1e6`.

**Data structures:** `fact[0..k]`, `invfact[0..k]`; loop `i=0..k` accumulating the signed sum; modular multiply/add.

**Short steps:** precompute factorials → for each `i` add/sub `C(k,i)*(k-i)^n` → print.

## Walkthrough (on a sample)

`n=6`, `k=4`:

| `i` | `C(4,i)` | `(4-i)^6` | signed term |
|-----|----------|-----------|-------------|
| 0 | 1 | `4^6 = 4096` | `+4096` |
| 1 | 4 | `3^6 = 729` | `-2916` |
| 2 | 6 | `2^6 = 64` | `+384` |
| 3 | 4 | `1^6 = 1` | `-4` |
| 4 | 1 | `0^6 = 0` | `0` |

Sum: `4096 - 2916 + 384 - 4 = 1560`.

## Pitfalls

- Using `0^n` carefully: for `n>=1`, `0^n = 0`; if you ever hit `n=0` it would be `1`, but here `n>=k>=1`.
- Computing `C(k,i)` naively in `O(k)` per `i` → `O(k^2)` TLE; need factorial table.
- Forgetting modular inverse / negative wrap when subtracting.
- Constraint `k <= n` is given; if `k > n` the answer would be `0` (impossible to cover).

## Complexity

- Time: `O(k + k log n)` for fact table + `k` modular exponentiations (or `O(k)` if you generate powers carefully)
- Space: `O(k)`

## Key takeaway

“Every label appears at least once” → surjections → classic inclusion-exclusion `sum (-1)^i C(k,i) (k-i)^n`.
