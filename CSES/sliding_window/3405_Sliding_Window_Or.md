# [3405 — Sliding Window Or](https://cses.fi/problemset/task/3405)

**Platform:** CSES  
**Source:** CSES Problem Set — Sliding Window Problems  
**Tags:** sliding window, bitwise OR, sparse table / two stacks  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Your task is to calculate the bitwise or of each window of `k` elements, from left to right.

In this problem the input data is large and it is created using a generator.

### Input

The first line contains two integers `n` and `k`: the number of elements and the size of the window.

The next line contains four integers `x`, `a`, `b` and `c`: the input generator parameters. The input is generated as follows:

- `x1 = x`
- `xi = (a * x_{i-1} + b) mod c` for `i = 2,3,...,n`

### Output

Print the xor of all window ors.

### Constraints

- `1 <= k <= n <= 1e7`
- `0 <= x, a, b <= 1e9`
- `1 <= c <= 1e9`

### Examples

**Input**
```
8 5
3 7 1 11
```

**Output**
```
4
```

Explanation: The input array is `[3,0,1,8,2,4,7,6]`. The windows are `[3,0,1,8,2]`, `[0,1,8,2,4]`, `[1,8,2,4,7]` and `[8,2,4,7,6]`, and their ors are `11`, `15`, `15` and `15`. Thus, the answer is `11 xor 15 xor 15 xor 15 = 4`.

---

## What this problem really is

Stream `n<=1e7` windows of OR. OR is **not** invertible (you cannot “subtract” a leaving value), so the sum/xor trick fails.

## What to use

| Tool | Why |
|------|-----|
| Two-stack / dual queue OR | Split window into a push stack and a pop stack, each with prefix ORs |
| Sparse table on blocks | Offline OR on static array if you materialize it (`O(n log n)` mem heavy at `1e7`) |
| Bitwise bit-count fenwick | Overkill; two stacks are `O(n)` |

## Approach

**Root idea:** maintain the window as `L` (queue front side) + `R` (back side). Push new values onto `R` with `prefOR`. To pop, if `L` empty, flip `R` into `L` rebuilding prefix ORs from the other end. Window OR = `L.topOR | R.topOR`.

**Mental picture:** two piles of numbers making up the window. Each pile knows the OR of everything in it via a running prefix. When the left pile is empty, pour the right pile over to refill it.

**How that becomes the method**

1. Structures: stack of values + parallel stack of prefix ORs for `in` and `out`.
2. `add(v)`: push onto `in`, `or_in = or_in_prev | v`.
3. `remove()`: if `out` empty, while `in`: pop to `out` computing prefix ORs; then pop `out`.
4. `query()` = `(out empty ? 0 : out.or) | (in empty ? 0 : in.or)`.
5. Stream LCG, add; after size `k`, also remove oldest each step; `ans ^= query()`.

Alternative: store the array (`~40MB` for `1e7` ints) and use a sparse table / disjoint sparse table for OR idempotent range queries in `O(1)`.

**Data structures:** two vectors for the dual-stack queue; or `int arr[n]` + ST.

## Walkthrough (on a sample)

Window ORs `11,15,15,15` → XOR `4`.

## Pitfalls

- Trying `wor |= enter; wor &= ~leave` (invalid for OR).  
- `O(nk)` recomputation.  
- Multiset of values (log factor + wrong for OR anyway).

## Complexity

- Time: `O(n)` with two-stack OR queue  
- Space: `O(k)`

## Key takeaway

**Sliding window OR needs an idempotent-range structure** (two-stack prefix OR or sparse table) — not an invertible running aggregate.
