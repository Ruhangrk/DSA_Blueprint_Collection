# [3398 — Permutation Rounds](https://cses.fi/problemset/task/3398)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** cycles, LCM, modular arithmetic  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a sorted array `[1, 2, ..., n]` and a permutation `p1, p2, ..., pn`. On each round, all elements move according to the permutation: the element at position `i` moves to position `pi`.

After how many rounds is the array sorted again for the first time?

### Input

The first line has an integer `n`.

The next line contains `n` integers `p1, p2, ..., pn`.

### Output

Print the number of rounds modulo `1e9+7`.

### Constraints

- `1 <= n <= 2e5`

### Examples

**Input**
```
8
5 3 2 6 4 1 8 7
```

**Output**
```
4
```

---

## What this problem really is

The map `i → p_i` is a permutation of positions. Applying it `t` times returns every value home iff `t` is a multiple of every cycle length. Minimal positive `t` = **LCM of all cycle lengths**, then take modulo `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| Cycle decomposition | Walk `i → p[i] → ...` for unvisited `i` |
| LCM via GCD | `lcm(a,b) = a / gcd(a,b) * b` |
| Compute LCM then `% MOD` | Do **not** take mod inside LCM (breaks correctness); use big integers / careful factorization, or since answer only needs `lcm % MOD`, multiply with gcd-reduction using 64-bit / `__int128` while values fit — cycle lengths ≤ `n ≤ 2e5`, but LCM of many can exceed 64-bit |

For large LCM: keep LCM as a map of max prime exponents, or use `__int128` carefully — with `n=2e5` LCM can be enormous, so **prime-exponent form** of LCM is the safe approach: for each cycle length, factor it and update max exponents; finally modpow-multiply into the answer.

## Approach

**Root idea:** values on a cycle of length `L` return to start every `L` rounds. Global return time is `lcm` of those `L`s.

**Mental picture:** several gears (cycles) spinning; the array is sorted when every gear has completed an integer number of turns — first sync time is the LCM.

**Steps**

1. Build cycles of the position permutation `p` (1-based).
2. Collect cycle lengths; compute `L = lcm` of them (via prime exponents or big-int LCM).
3. Print `L % MOD`.

## Walkthrough (on a sample)

Cycles produce lengths whose LCM is **4** — after 4 rounds the array is identity again.

## Pitfalls

- Taking `lcm % MOD` piecewise like `(a%MOD * b%MOD) / gcd` — wrong.
- Treating `p` as value permutation vs position map — follow “element at `i` goes to `p_i`”.
- Missing a cycle → wrong LCM.

## Complexity

- Time: `O(n log n)` with factorization of lengths  
- Space: `O(n)`

## Key takeaway

**Rounds until sorted again = LCM of permutation cycle lengths** (then `% 1e9+7`).
