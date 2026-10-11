# [3161 — GCD Subsets](https://cses.fi/problemset/task/3161)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Counting, inclusion-exclusion, divisors, harmonic sums  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. For each `k = 1..n`, count the non-empty subsets whose gcd is exactly `k`. Print the `n` counts modulo `1e9+7`.

### Input

- The first line has `n`.
- The second line has `x[1..n]`.

### Output

Print `n` integers: the count for `k = 1, 2, ..., n`, modulo `1e9+7`.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x[i] <= n`

### Examples

**Input**
```
5
5 4 4 2 3
```

**Output**
```
22 4 1 3 1
```

---

## What this problem really is

Counting subsets with an *exact* gcd is hard to do directly. Counting subsets where *every element is divisible by `k`* is easy, because you only need to know how many array elements are multiples of `k`. So the plan is:

1. Count "all elements divisible by `d`" subsets for every `d`.
2. Remove the over-counted ones, starting from the largest `d`.

## What to use

| Tool | Why |
|------|-----|
| Frequency array `freq[v]` | Counts how many times each value appears |
| `cnt[d]` = number of elements divisible by `d` | Sum of `freq[d], freq[2d], freq[3d], ...`, which costs `n/d` per `d` |
| `pow2[c]` precomputed | The number of non-empty subsets of `c` items is `2^c - 1` |
| Subtraction from larger multiples | Removes subsets whose gcd is a larger multiple of `k` |

## Approach

**Root idea:** a non-empty subset whose elements are all divisible by `k` has a gcd that is a multiple of `k`. So let `f[k]` be that count. Then

```
f[k] = 2^cnt[k] - 1
```

and `f[k]` equals the sum of `exact[m]` over all multiples `m` of `k`. Working from the largest `k` down, `exact[k] = f[k] - (exact[2k] + exact[3k] + ...)`.

**Mental picture:** imagine a row of buckets labeled by gcd value. The count `f[k]` pours into every bucket `k, 2k, 3k, ...`. To get only bucket `k`, subtract what already sits in the higher buckets. Going from the top down means each subtraction uses already-final numbers.

**Method**

1. Build `freq[v]` for `v = 1..n`.
2. For `d = 1..n`, compute `cnt[d] = freq[d] + freq[2d] + ...`.
3. For `k = n` down to `1`:
   - `exact[k] = pow2[cnt[k]] - 1`
   - subtract `exact[m]` for every multiple `m = 2k, 3k, ...` up to `n`.
4. Print `exact[1..n]` modulo `1e9+7`.

## Walkthrough (on the sample)

`x = 5 4 4 2 3`, `n = 5`. Frequencies: `freq[2]=1, freq[3]=1, freq[4]=2, freq[5]=1`.

Counts of elements divisible by `d`:

| d | elements divisible by d | cnt[d] | f[d] = 2^cnt - 1 |
|---|---|---|---|
| 1 | 5, 4, 4, 2, 3 | 5 | 31 |
| 2 | 4, 4, 2 | 3 | 7 |
| 3 | 3 | 1 | 1 |
| 4 | 4, 4 | 2 | 3 |
| 5 | 5 | 1 | 1 |

Exact counts from the top down:

| k | f[k] | subtract | exact[k] |
|---|---|---|---|
| 5 | 1 | none | 1 |
| 4 | 3 | none (8 > 5) | 3 |
| 3 | 1 | none (6 > 5) | 1 |
| 2 | 7 | exact[4] = 3 | 4 |
| 1 | 31 | exact[2]+exact[3]+exact[4]+exact[5] = 4+1+3+1 = 9 | 22 |

Output: `22 4 1 3 1`. Matches the expected output.

## Pitfalls

- Using `2^cnt` without subtracting, which counts subsets whose gcd is a multiple of `k`.
- Going from small `k` to large `k`. The subtraction needs the larger multiples to be final.
- Forgetting the `-1`. The empty subset must not be counted.
- Negative residues after subtraction. Add `MOD` before taking the remainder.
- Iterating multiples only up to `n`. Values are at most `n`, so larger multiples have count zero.

## Complexity

- Time: `O(n log n)` for the harmonic loops over multiples.
- Space: `O(n)`.

## Key takeaway

Count the easy quantity (subsets where every element is divisible by `k`), then subtract the over-counts from larger multiples. This "count at least, then remove the larger ones" pattern applies to any question about an exact gcd or lcm.
