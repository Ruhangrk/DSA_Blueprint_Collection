# [3194 — Xor Pyramid Diagonal](https://cses.fi/problemset/task/3194)

**Platform:** CSES  
**Source:** CSES Problem Set — Bitwise Operations  
**Tags:** XOR, Lucas, binomial mod 2  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a xor pyramid where each number is the xor of lower-left and lower-right numbers. Given the bottom row of the pyramid, your task is to find the leftmost number of each row.

### Input

The first line has an integer `n`: the size of the pyramid.

The next line has `n` integers `a1, a2, ..., an`: the bottom row of the pyramid.

### Output

Print `n` integers: the leftmost numbers of the rows from bottom to top.

### Constraints

- `1 <= n <= 2e5`
- `1 <= ai <= 1e9`

### Examples

**Input**
```
8
2 10 5 12 9 5 1 5
```

**Output**
```
2 8 7 1 11 4 15 9
```

---

## What this problem really is

Same xor pyramid as Peak, but output the entire left spine (bottom → top), not only the apex.

## What to use

| Tool | Why |
|------|-----|
| Peak-on-prefix | Left cell above a base of length `len` = peak of `a[1..len]` |
| Submask test | `C(len-1, j)` odd iff `j` is a submask of `len-1` |
| Contribution flip | Each `a[j]` XORs into all left-answers whose `len-1` supermasks `j-1` |

## Approach

**Root idea:** the leftmost entry on the row whose base width is `len` equals `xor_j a[j]` over those `j` with `(j-1) & (len-1) == (j-1)`. That is exactly the Peak formula on the prefix `a[1..len]`.

**Mental picture:** climbing the left rail only ever mixes a prefix of the bottom row; each rail height is a “mini peak” of that prefix.

**How that becomes the method**

1. Allocate `ans[1..n]` (answer for each prefix length / left cell from bottom).
2. For each bottom index `j = 1..n`, let `sub = j-1`. Enumerate every supermask `sm` of `sub` with `sm <= n-1` (standard `for (int sm = sub; ; sm = (sm+1) | sub)` until break). For each such `sm`, do `ans[sm+1] ^= a[j]`.
3. Print `ans[1], ans[2], ..., ans[n]` (bottom-to-top left cells: width `n` down to width `1`? Careful with order).

Order check: sample wants bottom-to-top, so first printed is leftmost of bottom row = `a1` = peak of length `1` = `ans[1]`, then left of width-`(n-1)` row after one up-step… Actually after one level up the row has length `n-1`, its left cell is peak of some window — for the **left spine**, the row with `len` cells left on the bottom under the left path uses prefix length `len`. Bottom row left = `ans[1]`? Bottom leftmost is `a1`, which is peak of prefix length `1` → `ans[1]`. Next upward left sees prefix length `2` → `ans[2]`. Top is `ans[n]`. Sample first value `2 = a1 = ans[1]`, last `9 = ans[8]`. So print `ans[1]..ans[n]`.

Supermask enumeration total time is `O(sum 2^{n-bits})` ≈ OK enough for `n<=2e5` with care? Worst can be heavy; an equivalent `O(n log n)` implementation applies the same XORs via bit-DP / fenwick. For intuition, the contribution view is enough; implement with efficient supermask loops or SOS DP.

**Data structures:** `ans[n+1]` initialized to `0`.

## Walkthrough (on a sample)

`ans[1]=2`, `ans[2]=2^10=8`, `ans[3]=2^5=7` (submasks of `2`), …, `ans[8]=9`.

## Pitfalls

- Printing top-to-bottom by mistake.  
- Building every pyramid row explicitly `O(n^2)`.  
- Wrong submask bound `sm < n` vs `sm <= n-1`.

## Complexity

- Time: about `O(n log n)` with a tight implementation / SOS  
- Space: `O(n)`

## Key takeaway

**Left diagonal = peak-of-prefix for every length** — XOR `a[j]` into all prefix-lengths whose `(len-1)` supermasks `(j-1)`.
