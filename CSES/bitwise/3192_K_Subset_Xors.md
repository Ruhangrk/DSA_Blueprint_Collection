# [3192 — K Subset Xors](https://cses.fi/problemset/task/3192)

**Platform:** CSES  
**Source:** CSES Problem Set — Bitwise Operations  
**Tags:** XOR basis, k smallest, priority queue  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Consider the xors of all `2^n` subsets of the array (including the empty subset with xor equal to zero).

Your task is to find the `k` smallest subset xors.

### Input

The first line has two integers `n` and `k`: the size of the array and the number of subset xors `k`.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the array.

### Output

Print `k` integers: the `k` smallest subset xors in increasing order.

### Constraints

- `1 <= n <= 2e5`
- `1 <= k <= min(2^n, 2e5)`
- `0 <= xi <= 1e9`

### Examples

**Input**
```
4 9
3 5 14 8
```

**Output**
```
0 0 3 3 5 5 6 6 8
```

---

## What this problem really is

List the `k` smallest values in the multiset of all subset XORs. Note: sample shows duplicates — different subsets can share the same XOR, and you list with multiplicity? Looking at sample: `0` twice, `3` twice… So it's the `k` smallest in the multiset of `2^n` subset xors (with multiplicity), not distinct.

Wait - empty and maybe another subset both 0. Yes, multiplicity over subsets.

Actually re-read: "the k smallest subset xors" and sample has duplicates. So sorted list of all `2^n` xor values (one per subset), take first `k`.

## What to use

| Tool | Why |
|------|-----|
| XOR basis + free zeros | Rank `r`; each xor appears `2^(n-r)` times |
| Generate distinct span in order | Then expand multiplicities |
| Priority queue / DFS on basis | Produce distinct xors ascending until enough |

## Approach

**Root idea:** first build the basis (`r` independent vectors). Every distinct reachable XOR appears equally often: `mult = 2^(n-r)` times. So the answer is: take the sorted distinct subset xors `v0 < v1 < ...`, and emit each `mult` times, until `k` numbers are printed.

**Mental picture:** the span is a sorted list of `2^r` distinct values; each is copied `2^(n-r)` times. Walk that tape from the left for `k` entries.

**How that becomes the method**

1. Insert all into XOR basis; `r = rank`; `mult = 1LL << (n-r)` (careful if `n-r >= 63` — then `k` still `<=2e5` so you only need multiplicity conceptually when emitting).
2. Generate all distinct span elements in increasing order (priority queue starting from `0`, XOR with basis vectors larger than the last bit used — standard “k smallest in XOR span” / enumerate via basis).
3. For each distinct value in order, append it `min(mult, remaining)` times to the output.

Enumerating distinct: keep a min-heap of reachable values; pop, push `val xor basis[i]` for basis elements that increase the value with a structured index to avoid duplicates (classic).

**Data structures:** basis; min-heap of `(value, next_basis_index)`; output vector.

## Walkthrough (on a sample)

Many subsets; the nine smallest multiset values are `0,0,3,3,5,5,6,6,8`.

## Pitfalls

- Printing distinct only (sample needs multiplicity).  
- Generating `2^n` explicitly.  
- Overflow on `1<<(n-r)`.

## Complexity

- Time: `O(n B + k log k)` typical  
- Space: `O(k + B)`

## Key takeaway

**k smallest subset XORs (with multiplicity) = sorted XOR-span, each repeated `2^(n-rank)` times.**
