# [3397 — Permutation Order](https://cses.fi/problemset/task/3397)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** factorial number system, ranking permutations  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Let `p(n,k)` denote the `k` th permutation (in lexicographical order) of `1 ... n`. For example, `p(4,1)=[1,2,3,4]` and `p(4,2)=[1,2,4,3]`.

Your task is to process two types of tests:

1. Given `n` and `k`, find `p(n,k)`
2. Given `n` and `p(n,k)`, find `k`

### Input

The first line has an integer `t`: the number of tests.

Each test is either `"1 n k"` or `"2 n p(n,k)"`.

### Output

For each test, print the answer according to the example.

### Constraints

- `1 <= t <= 1000`
- `1 <= n <= 20`
- `1 <= k <= n!`

### Examples

**Input**
```
6
1 4 1
1 4 2
2 4 1 2 3 4
2 4 1 2 4 3
1 5 42
2 5 2 4 5 3 1
```

**Output**
```
1 2 3 4
1 2 4 3
1
2
2 4 5 3 1
42
```

---

## What this problem really is

Bijection between rank `k` (1-based) and permutations of `1..n` in lex order. `n <= 20` so `n!` fits in 64-bit.

## What to use

| Tool | Why |
|------|-----|
| Factorial digit system | Rank → choice of next unused element |
| List/set of remaining numbers | Pick the `idx`-th unused |
| `__int128` / `unsigned long long` | `20!` fits in 64-bit (`2432902008176640000`) |

## Approach

**Root idea:** among permutations of `n` elements, the first position is constant for blocks of size `(n-1)!`. The block index picks which remaining number goes next; recurse on the rest with `k` reduced inside the block.

**Type 1 — unrank `k` (1-based):**

1. Remaining = `{1,2,...,n}`.
2. For position from left: `block = (n-pos)!`; `idx = (k-1) / block`; place the `idx`-th remaining number; `k = (k-1) % block + 1` (or 0-based throughout).

**Type 2 — rank permutation:**

1. Remaining = `{1..n}`.
2. `k = 1`. For each value `p[i]` in order: let `idx` = how many remaining numbers are `< p[i]`; `k += idx * (n-i)!`; remove `p[i]`.

**Mental picture:** a factorial odometer — each digit chooses which unused symbol is next.

## Walkthrough (on a sample)

`p(4,2)`: first digit blocks of `3!=6`… with `k=2`, first element still `1`, then among `{2,3,4}` the second perm of the rest is `2,4,3` → `[1,2,4,3]`.

## Pitfalls

- 0-based vs 1-based `k`.
- Using `int` for factorials when `n` approaches 20.
- Not removing used numbers from the candidate list.

## Complexity

- Time: `O(t * n^2)` with naive list, fine for `n<=20`  
- Space: `O(n)`

## Key takeaway

**Lex rank ↔ permutation = factorial number system** on the unused elements.
