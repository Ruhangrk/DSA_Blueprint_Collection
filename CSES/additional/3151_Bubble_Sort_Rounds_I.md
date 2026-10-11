# [3151 — Bubble Sort Rounds I](https://cses.fi/problemset/task/3151)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** bubble sort, sorting, fenwick / ranking  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Bubble sort is a sorting algorithm that consists of a number of rounds. On each round the algorithm scans the array from left to right and swaps any adjacent elements that are in the wrong order.

Given an array of `n` integers, calculate the number of bubble sort rounds needed to sort the array.

### Input

The first line has an integer `n`: the array size.

The next line has `n` integers `x1,x2,...,xn`: the array contents.

### Output

Print one integer: the number of rounds.

### Constraints

- `1 <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
5
3 2 4 1 4
```

**Output**
```
3
```

Explanation: After rounds the array becomes `[2,3,1,4,4]`, then `[2,1,3,4,4]`, then `[1,2,3,4,4]`.

---

## What this problem really is

How many L→R bubble-sort passes until the array is sorted. Equivalently: the maximum leftward distance any element must travel (with stable targets when values tie).

## What to use

| Tool | Why |
|------|-----|
| Stable target positions | Bubble sort is stable — equal values keep order |
| Left-move distance | Each round moves an element at most one index left |
| Max over elements | Rounds = furthest any element still has to go left |

## Approach

**Root idea:** in one round, every inversion with an adjacent larger-left / smaller-right swaps once, so each element can move at most one step toward its final place from the left. The number of rounds is the maximum of `(currentIndex - finalIndex)` over elements that finish left of their start.

**Mental picture:** small values bubble left one seat per round. The stubborn smallest value buried deep on the right dictates the round count.

**How that becomes the method**

1. Build pairs `(value, originalIndex)` and stably sort by value (then index) to get final positions `0..n-1`.
2. For each element, `need = originalIndex - finalIndex` (0 if negative).
3. Answer = maximum `need` (0 if already sorted).

Equivalent view: for each index `i`, if `x[i]`’s stable rank is `r`, it contributes `i-r` when `i>r`.

**Data structures:** vector of pairs; sort; `long long` not required for the max distance (`<=n`).

## Walkthrough (on a sample)

`[3,2,4,1,4]`. Stable sorted order of indices: `1` from pos3 → final0 (need 3); `2` from pos1 → final1 (need 0); `3` from pos0 → final2; first `4` from pos2 → final3; second `4` from pos4 → final4. Max need = `3`.

## Pitfalls

- Ignoring stability (wrong finals for duplicates).
- Using inversion count (total swaps ≠ number of rounds).
- Simulating bubble sort in `O(n^2)` — TLE at `2e5`.

## Complexity

- Time: `O(n log n)`
- Space: `O(n)`

## Key takeaway

Bubble-sort rounds = max leftward travel of any element to its stable sorted position.
