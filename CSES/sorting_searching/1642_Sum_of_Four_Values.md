# [1642 — Sum of Four Values](https://cses.fi/problemset/task/1642)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** hashing, meet in the middle, two pointers  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers, and your task is to find four values (at distinct positions) whose sum is `x`.

### Input

The first input line has two integers `n` and `x`: the array size and the target sum.

The second line has `n` integers `a1, a2, ..., an`: the array values.

### Output

Print four integers: the positions of the values. If there are several solutions, you may print any of them. If there are no solutions, print `IMPOSSIBLE`.

### Constraints

- `1 <= n <= 1000`
- `1 <= x, ai <= 1e9`

### Examples

**Input**
```
8 15
3 2 5 8 1 3 2 3
```

**Output**
```
2 4 6 7
```

---

## What this problem really is

Four distinct indices summing to `x`. `n <= 1000` allows `O(n^2)` pair enumeration with a hash map (not `O(n^4)`).

## What to use

| Tool | Why |
|------|-----|
| All unordered pairs `i < j` | `O(n^2)` pairs, each with sum and two indices |
| Hash map `sum -> pair of indices` | Look up `x - sum` for a complementary pair |
| Index-disjoint check | The four positions must be distinct |
| Or sort + nested two pointers | Also `O(n^3)` / careful `O(n^2 log)` variants |

## Approach

**Root idea:** split four numbers into two pairs. Enumerate every pair `(i,j)`, and look for a previously stored pair whose sum is `x - (a[i]+a[j])` and whose indices don’t collide with `i,j`.

**Mental picture:** bag of “two-number chunks” labeled by their sum; for each new chunk, ask if the matching chunk is already in the bag without sharing people.

**Data structures**

- `unordered_map` / `map` from pair-sum → one (or few) index-pairs.
- Safe pattern: first put all pairs into a vector, sort by sum, two-pointer on that — or build the map while scanning pairs with `j` increasing carefully.

**Reliable `O(n^2)` pattern**

1. For `i = 0..n-1`:
   - For `j = i+1..n-1`: look up `need = x - a[i] - a[j]` in a map of pairs formed only from indices `< i` (so disjoint). If found, print all four indices.
   - After finishing all `j` for this `i`, insert all pairs `(k,i)` for `k < i` into the map (sums `a[k]+a[i]`).

This ensures the stored pairs only use indices strictly left of `i`, so no overlap with `{i,j}`.

**Steps**

1. Run the nested loops above.
2. If no hit: `IMPOSSIBLE`.

## Walkthrough (on a sample)

`x=15`, one solution `2+8+3+2` at positions `2,4,6,7` (1-based). Pair `{2,8}` sum 10 and pair `{3,2}` sum 5 complement to 15 with four distinct indices.

## Pitfalls

- Storing pairs that share an index with the query pair.
- `O(n^3)` with three nested loops — may pass `n=1000` barely but map-of-pairs is cleaner/`O(n^2)`.
- Hash collisions / using `map` if TL is tight — usually fine at `n=1000`.

## Complexity

- Time: `O(n^2)` expected with hash  
- Space: `O(n^2)` pairs in the worst case (can store one pair per sum if you only need any solution — still careful with index conflicts; storing one pair per sum can miss a valid disjoint pair — for “any solution” keep a list or the left-only insertion pattern above which stores `O(n^2)` entries)

## Key takeaway

**4-sum = two pairs:** enumerate pair `(i,j)`, look up complementary sum from pairs that only use indices to the left of `i`.
