# [3193 — Square Subsets](https://cses.fi/problemset/task/3193)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Number theory, square-free part, GF(2) linear algebra, XOR basis, counting subsets  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, count the number of subsets whose product is a perfect square. The empty subset counts, since its product is 1.

### Input

- The first line has an integer `n`: the size of the array.
- The next line has `n` integers `x[1], ..., x[n]`.

### Output

Print one integer: the answer modulo `1e9 + 7`.

### Constraints

- `1 <= n <= 5000`
- `1 <= x[i] <= 5000`

### Examples

**Input**
```
4
2 2 3 6
```

**Output**
```
4
```

---

## What this problem really is

A product is a square exactly when every prime appears with an even exponent. Parity is the only thing that matters. So each number collapses to a parity vector over primes, and we count subsets whose parity vectors sum to zero modulo 2.

## What to use

| Tool | Why |
|------|-----|
| Square-free part of each `x[i]` | Keeps only primes with odd exponent. Two numbers with the same square-free part are "equal" for this problem. |
| Vectors over GF(2), one coordinate per prime `<= 5000` | Multiplying numbers = adding parity vectors |
| Gaussian elimination / XOR basis | Gives the rank `r` of the vectors |
| Counting formula `2^(n - r)` | Number of subsets whose XOR is zero, for `n` vectors of rank `r` |

Enumerating subsets is `2^5000`, which is impossible. The linear-algebra count avoids it.

## Approach

**Root idea:** a subset has a square product iff the XOR of its parity vectors is zero. For `n` vectors over GF(2) with rank `r`, the number of subsets with XOR zero is exactly `2^(n - r)`. This holds including the empty subset and including zero vectors.

**Mental picture:** think of the `n` vectors as arrows in a space of dimension `r` that they span. Choose a basis from the arrows; the other `n - r` arrows can each be "absorbed" as a combination of basis arrows. Each of those `n - r` free arrows can be in or out of the subset independently. Once the free arrows are chosen, the basis arrows used are forced to cancel exactly once, so there is exactly one valid choice of basis-part per free-part. Hence `2^(n - r)`.

**How that becomes the method**

1. For each `x`, factor out squares to get its square-free part (a bitmask over primes `<= 5000`). A simple way: for each `x`, divide by each prime `p` while `p*p` divides, tracking parity of exponent of each prime.
2. Build a parity bit-vector per element. The vector length is the number of primes `<= 5000` (669 primes).
3. Insert each vector into an XOR basis (a list of vectors with distinct leading bits). If the vector reduces to zero, it adds no rank. Otherwise it adds one to the rank.
4. Answer = `2^(n - rank) mod 1e9 + 7`.

**Data structures:**
- `std::bitset<669>` for each parity vector.
- Basis: array indexed by leading bit position, `basis[b]` holds the vector with leading bit `b` (or empty).

## Walkthrough (on a sample)

Input `2 2 3 6`. Primes are 2 and 3 (only the first two coordinates matter here).

Parity vectors (coordinates `[2, 3]`):

- `2`: exponent of 2 is 1 (odd), of 3 is 0. Vector `[1,0]`.
- `2`: `[1,0]`.
- `3`: `[0,1]`.
- `6 = 2 * 3`: `[1,1]`.

Insert into basis:

- Insert `[1,0]`: leading bit at coordinate 2. Basis slot for 2 is empty, store it. Rank = 1.
- Insert `[1,0]`: reduce by slot 2 → `[0,0]`. Zero, rank stays 1.
- Insert `[0,1]`: leading bit at coordinate 3. Slot empty, store. Rank = 2.
- Insert `[1,1]`: reduce by slot 2 → `[0,1]`, then reduce by slot 3 → `[0,0]`. Zero, rank stays 2.

Rank `r = 2`, `n = 4`, answer `2^(4 - 2) = 4`.

Check by listing: the valid subsets are `{}`, `{2_a, 2_b}` (product 4), `{2_a, 3, 6}` (product 36), and `{2_b, 3, 6}` (product 36). Total 4 ✓.

## Pitfalls

- Forgetting the empty subset. It is counted by the formula (`2^(n-r)` includes it), so do not add 1 separately.
- Treating equal numbers as conflicting. They are not; `2` and `2` together form a square `4`.
- Using the full number rather than the parity vector. Only exponent parity matters.
- Enumerating primes up to `x` for every element without caching. Precompute the 669 primes once, or precompute square-free parts for all `x <= 5000` with a sieve.
- Forgetting to reduce `2^(n-r)` modulo `1e9+7`.
- Zero parity vectors (perfect squares like `1`, `4`, `9`) do not change rank but do double the count through `2^(n-r)`. That is correct.

## Complexity

- Time: `O(n * P * P / 64)` for basis insertions, where `P = 669` primes and `64`-bit words. Here `P / 64 ~ 11` words, so about `5000 * 669 * 11 ~ 4e7` word operations. Well within 1 s.
- Space: `O(n * P / 64 + P)`.

## Key takeaway

When the question is "product is a square" over many numbers, reduce every number to its parity vector over GF(2). Then count subsets with zero XOR as `2^(n - rank)`.
