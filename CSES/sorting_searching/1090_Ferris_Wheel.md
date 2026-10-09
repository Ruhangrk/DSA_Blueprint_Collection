# [1090 — Ferris Wheel](https://cses.fi/problemset/task/1090)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** sorting, two pointers, greedy  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` children who want to go to a Ferris wheel, and your task is to find a gondola for each child.

Each gondola may have one or two children in it, and in addition, the total weight in a gondola may not exceed `x`. You know the weight of every child.

What is the minimum number of gondolas needed for the children?

### Input

The first input line contains two integers `n` and `x`: the number of children and the maximum allowed weight.

The next line contains `n` integers `p1, p2, ..., pn`: the weight of each child.

### Output

Print one integer: the minimum number of gondolas.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x <= 1e9`
- `1 <= pi <= x`

### Examples

**Input**
```
4 10
7 2 3 9
```

**Output**
```
3
```

---

## What this problem really is

Pack `n` weights into bins of capacity `x`, each bin holds **at most 2** items. Minimize the number of bins.

## What to use

| Tool | Why |
|------|-----|
| Sort weights | Pair lightest with heaviest greedily |
| Two pointers | `i` = lightest free, `j` = heaviest free |
| Greedy pairing | Heaviest either shares with lightest if possible, else rides alone |

## Approach

**Root idea:** after sorting, the heaviest remaining child should share with the lightest remaining child if their sum is `<= x`; otherwise the heaviest needs a solo gondola. That minimizes wasted capacity for the hard cases.

**Mental picture:** line kids light→heavy. Always seat the heaviest; try to tuck the lightest beside them if the gondola still holds.

**Data structures**

- Sorted array `p[0..n)`.
- Pointers `i = 0`, `j = n-1`, answer `gondolas = 0`.

**Steps**

1. Sort `p` ascending.
2. While `i <= j`:
   - Always place `p[j]` in a new gondola (`gondolas++`).
   - If `i < j` and `p[i] + p[j] <= x`, also place `p[i]` in that gondola (`i++`).
   - `j--`.
3. Print `gondolas`.

(Equivalent: start with `n` gondolas and decrement whenever a successful pair is made.)

## Walkthrough (on a sample)

`x=10`, sorted `2,3,7,9`.

- Heaviest `9` + lightest `2` = `11 > 10` → `9` alone. Left `2,3,7`.
- `7+2=9 <= 10` → pair `7` with `2`. Left `3`.
- `3` alone.

Total **3** gondolas.

## Pitfalls

- Allowing 3+ kids in one gondola — forbidden.
- Only checking adjacent pairs after sort without the light↔heavy strategy — can use more gondolas.
- Forgetting `i == j` (last child alone).
- Constraint `pi <= x` guarantees every child fits alone.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**At-most-two packing under a weight cap = sort + pair lightest with heaviest** when they fit; otherwise heaviest takes a solo gondola.
