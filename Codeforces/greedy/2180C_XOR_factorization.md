# [2180C — XOR-factorization](https://codeforces.com/problemset/problem/2180/C)

**Platform:** Codeforces
**Source:** Codeforces Global Round 31 (Div. 1 + Div. 2)
**Tags:** bitmasks, constructive algorithms, dp, greedy, number theory
**Difficulty / Rating:** 1900
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Global Round 31 Tutorial #1 (Problem C section)](https://codeforces.com/blog/entry/149318)

---

## Problem Statement

Ostad thinks that the usual way of factoring numbers is too mathematical, so he invented a new notion called XOR-factorization, which is more computer-science-like. For a given integer `n`, a sequence of integers `a[1], a[2], ..., a[k]` with `0 <= a[i] <= n` for all `i` is called a XOR-factorization of `n` if and only if

`a[1] XOR a[2] XOR ... XOR a[k] = n`,

where `XOR` denotes the bitwise XOR operation.

You are given integers `n` and `k`. Find a XOR-factorization `a[1], a[2], ..., a[k]` of `n` that maximizes the sum `a[1] + a[2] + ... + a[k]`.

It can be proven that under the problem conditions, a XOR-factorization always exists.

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`). The description of the test cases follows.

Each of the next `t` lines contains two integers `n` and `k` (`1 <= n <= 1e9`, `1 <= k <= 1e5`).

It is guaranteed that the sum of `k` over all test cases does not exceed `1e5`.

### Output

For each test case, output `k` integers `a[1], a[2], ..., a[k]` such that `0 <= a[i] <= n`.

We can show that an answer always exists. If there are multiple valid answers, you may print any of them in any order.

### Constraints

- `1 <= t <= 1e4`
- `1 <= n <= 1e9`
- `1 <= k <= 1e5`
- Sum of `k` over all test cases `<= 1e5`

### Examples

Input:

```
4
5 4
4 3
8 2
1 1
```

Output:

```
1 4 5 5
4 4 4
0 8
1
```

### Note

In the first test case, we can factor `5` as `1 XOR 4 XOR 5 XOR 5` with a sum of `15`, and it can be shown that no other XOR-factorization has a higher sum.

In the second test case, we can factor `4` as `4 XOR 4 XOR 4` with a sum of `12`, which is trivially the maximum possible.

---

## What this problem really is

Write `n` as the XOR of exactly `k` integers in `0..n`, maximizing their sum.

## What to use

| Tool | Why |
|------|-----|
| Bit-by-bit greedy | Higher bits dominate the sum, so decide each bit from high to low. |
| Parity of ones | XOR equals `n` fixes the parity of how many `a[i]` have each bit set. |
| Tight / loose sets | Tracks which `a[i]` still match `n`'s prefix (tight) and which are already smaller (loose), so we never exceed `n`. |

## Approach

**Root idea (from the editorial):** Build every `a[i]` from the highest bit down to bit `0`. At each bit, the XOR condition fixes the parity of the number of ones, and the sum wants as many ones as possible. Prefer making tight indices become loose when you must place a zero, because loose indices can take ones later on bits where `n` has a zero.

**Mental picture:** Imagine `k` counters under a binary ceiling `n`. For each bit from the top: if `n` has a `1` there, an odd number of counters must light that bit — take the largest odd count `<= k`. If `n` has a `0`, an even number of counters must light it — but only counters already below the ceiling (loose) are allowed to light it.

**How that becomes the method:**
1. Let `o(x)` be the largest odd integer `<= x`, and `e(x)` the largest even integer `<= x`.
2. Start with all `a[i] = 0`, all indices tight (`T = {1..k}`), loose set empty.
3. For bit `b` from high down to `0`:
   - If bit `b` of `n` is `1`: place ones in `o(k)` of the numbers. Prefer putting the forced zero (when `k` is even) on a tight index, moving it to loose.
   - If bit `b` of `n` is `0`: only loose indices may get a one; place ones in `e(|L|)` of them.
4. Shortcut used in the editorial implementation: if `k` is odd, every `a[i] = n` works (XOR of an odd number of copies of `n` is `n`, and the sum is maximal). If `k` is even, run the bit construction above (equivalently: walk bits of `n`, leave one index without the high ones to create loose room, then fill even numbers of ones into the loose group on zero-bits of `n`).

**Data structures:**
- An array `a[0..k-1]`.
- A counter of how many indices have already been made loose (or explicit sets `T` / `L`).

## Walkthrough (on a sample)

Second test: `n = 4`, `k = 3` (odd). Shortcut: output `4 4 4`. XOR is `4`, sum `12`, which is `k * n`, hence optimal.

First test: `n = 5` (`101` in binary), `k = 4` (even). One valid editorial-style answer is `1 4 5 5`:
- XOR: `1 XOR 4 XOR 5 XOR 5 = 1 XOR 4 XOR 0 = 5`.
- Sum `15`. Each value is `<= 5`.

## Pitfalls

- **Ignoring `a[i] <= n`:** Placing a one on a tight index when `n` has a zero there makes `a[i] > n`.
- **Wrong parity of ones:** XOR fails if the number of ones at a bit has the wrong parity.
- **Not maximizing ones at high bits:** Leaving out an optional pair of ones at bit `b` loses `2 * 2^b`, which lower bits cannot recover.
- **Large `n`, large sum of `k`:** Build in `O(k log n)`, never enumerate up to `n`.

## Complexity

- Time: `O(k log n)` per test. Over all tests, `O((sum of k) * log n)`.
- Space: `O(k)`.

## Key takeaway

Decide bits from high to low: keep the XOR parity, place as many ones as `a[i] <= n` allows, and spend forced zeros on tight indices to unlock more room later.
