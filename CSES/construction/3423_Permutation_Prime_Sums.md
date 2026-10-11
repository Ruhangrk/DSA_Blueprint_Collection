# [3423 — Permutation Prime Sums](https://cses.fi/problemset/task/3423)

**Platform:** CSES  
**Source:** CSES Problem Set — Construction Problems  
**Tags:** construction, primes, permutations  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given `n`, create two permutations `a` and `b` of size `n` such that `a[i]+b[i]` is prime for every `i=1..n`.

### Input

The only line has an integer `n`.

### Output

Print two permutations. You can print any valid solution. If there are no solutions, print `IMPOSSIBLE`.

### Constraints

- `1 <= n <= 1e5`

### Examples

**Input**
```
5
```

**Output**
```
2 1 3 5 4
5 1 4 2 3
```

Explanation: Sums `7,2,7,7,7` are all prime.

---

## What this problem really is

Pair each position’s two values from two permutations so every pairwise sum is prime.

## What to use

| Tool | Why |
|------|-----|
| Goldbach-ish / parity | Odd+even = odd (can be prime >2); odd+odd = even >2 not prime |
| Match odds to evens | So every sum is odd (except involving `1` and `2`) |
| `IMPOSSIBLE` cases | Too many odds vs evens to pair |

## Approach

**Root idea:** for `sum >= 4` to be prime it must be odd ⇒ one even and one odd. Among `1..n` there are `ceil(n/2)` odds and `floor(n/2)` evens. You need a perfect pairing between the two permutations’ entries at each index — essentially a bijection from positions where odds in `a` meet evens in `b` and vice versa.

**Mental picture:** paint odds red and evens blue; every column needs one red and one blue across the two rows.

**How that becomes the method**

1. If `n=1`: only `1+1=2` prime → both perms `[1]`.  
2. Count odds/evens. You can set `a = 1..n` and seek a derangement/`b` such that each `a[i]+b[i]` is prime — equivalent to a perfect matching in the bipartite graph “value ↔ value” with prime sums, then place matching as two rows.  
3. Construction: put all odds in the first half of `a` and evens in the second (or interleave); set `b` so each odd faces an even with prime sum (e.g. match `x` with `p-x` for a fixed small prime `p` larger than `n` when possible — Goldbach for odd targets).  
4. Known workable pattern: for many `n`, `a = identity`, `b[i] = n+1-i` and check/adjust swaps so sums are prime; or match via edges `i+j` prime.

Simple constructive approach often used: create bipartite matching between `{1..n}` and `{1..n}` with edges when `i+j` is prime (degree is decent by Dirichlet); Hall’s theorem typically holds for `n>1` except small failures — run greedy matching / flow. For intuition page: **must pair odd with even**; build any two perms realizing an odd-even pairing at each index with prime sums (fix small partner primes).

**Data structures:** sieve up to `2n` for primality; arrays for the two perms.

## Walkthrough (on a sample)

One odd-even pairing gives all-prime column sums as in the sample.

## Pitfalls

- Odd+odd columns (even sums >2).  
- Reusing a number in a permutation.  
- Missing `n=1` special case.

## Complexity

- Time: `O(n log n)` or `O(n^2)` matching for small patterns; sieve `O(n log log n)`  
- Space: `O(n)`

## Key takeaway

**Prime column sums ⇒ odd+even at every index**; build two perms realizing such a pairing with prime sums (else `IMPOSSIBLE`).
