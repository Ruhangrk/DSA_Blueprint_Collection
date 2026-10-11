# [1706 — School Excursion](https://cses.fi/problemset/task/1706)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Union-find (DSU), connected components, subset sum, bitset, bounded knapsack  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

`n` children each choose one of two attractions. There are `m` pairs of children who must choose the same attraction. Find every possible number of children who visit Korkeasaari.

### Input

- The first line has `n` and `m`.
- The next `m` lines each have `a` and `b`: children `a` and `b` must choose the same attraction.

### Output

Print a bit string of length `n`. Bit `i` (1-indexed) is `1` if exactly `i` children can visit Korkeasaari, and `0` otherwise.

### Constraints

- `1 <= n <= 1e5`
- `0 <= m <= 1e5`

### Examples

**Input**
```
5 3
1 2
2 3
1 5
```

**Output**
```
10011
```

**Explanation:** The count can be `1`, `4` or `5`.

---

## What this problem really is

Children linked by a pair must choose the same attraction, so they form a group that moves together. Each group has a size, and either all of it goes to Korkeasaari or none of it does. The question becomes: which sums of group sizes are possible? That is a subset-sum question on the group sizes.

## What to use

| Tool | Why |
|------|-----|
| Union-find (DSU) | Merges children linked by a pair into groups, and gives each group's size |
| Subset-sum bitset | Records every reachable total in one bitset, updated per group |
| Binary splitting on repeated sizes | Many groups may share a size, so combine them to keep the work small |

## Approach

**Root idea:** a group of children linked by pairs always goes together. So choose a subset of groups, and the count of Korkeasaari visitors is the sum of the chosen groups' sizes. The answer is the set of all achievable sums.

**Mental picture:** imagine each group as a stack of coins with a face value equal to its size. You can flip any subset of stacks to the Korkeasaari side. A bitset with one bit per total is a row of lights: bit `t` is on if some subset of stacks sums to `t`. Adding a stack of size `s` turns on bit `t + s` for every bit `t` already on. Doing this for every stack gives all reachable totals.

**How that becomes the method**

1. Use DSU to merge each pair `(a, b)`. Record each component's size.
2. Count how many components have each size. Let `cnt[s]` be the number of components of size `s`.
3. Start with bitset `R` having only bit `0` set.
4. For each size `s` with count `c`, split `c` into powers of two (`1, 2, 4, ...` with the remainder). Each chunk of `q` groups acts as one item of size `q * s`, shifted into `R` with `R |= R << (q * s)`.
5. Output, for `i = 1..n`, the bit `R[i]`.

Binary splitting works because any count `0..c` can be formed from the chunks, so one shift per chunk covers all choices for that size.

**Data structures:** DSU with size tracking, an array `cnt`, and a bitset of size `n + 1`.

## Walkthrough (on a sample)

`n = 5`, pairs `(1,2)`, `(2,3)`, `(1,5)`.

**DSU merges:**
- Merge `1` and `2`: group `{1,2}`.
- Merge `2` and `3`: group `{1,2,3}`.
- Merge `1` and `5`: group `{1,2,3,5}`.

Components: `{1,2,3,5}` of size `4`, and `{4}` of size `1`.

Counts: `cnt[4] = 1`, `cnt[1] = 1`.

**Subset sums:**
- Start: `R = {0}`.
- Add the group of size `4`: `R = {0, 4}`.
- Add the group of size `1`: `R = {0, 1, 4, 5}`.

**Output** bits `1..5`:
- `1`: in `R`, so `1`.
- `2`: not in `R`, so `0`.
- `3`: not in `R`, so `0`.
- `4`: in `R`, so `1`.
- `5`: in `R`, so `1`.

Output: `10011`. Matches.

## Pitfalls

- Forgetting that the bitset only needs sums up to `n`. Sizing it smaller than `n + 1` loses valid answers.
- Treating each child as independent. Pairs force groups, so the count must be built from group sizes.
- Doing one shift per group without grouping. With up to `1e5` groups, that is too slow when many share a size; binary splitting keeps it fast.
- Forgetting isolated children. A child with no pairs is a group of size `1`.
- Printing bit `0`. The output starts at index `1`.

## Complexity

- Time: `O(m α(n) + (number of chunks) * n / 64)`. The number of chunks is `O(sqrt(n) log n)`, so this is fast for `n = 1e5`.
- Space: `O(n)`.

## Key takeaway

Merge forced-together items into groups with a DSU, then the reachable totals are a subset sum over group sizes. Use a bitset with binary splitting for repeated sizes.
