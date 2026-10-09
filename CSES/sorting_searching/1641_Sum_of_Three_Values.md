# [1641 — Sum of Three Values](https://cses.fi/problemset/task/1641)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** two pointers, sorting, meet in the middle style  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers, and your task is to find three values (at distinct positions) whose sum is `x`.

### Input

The first input line has two integers `n` and `x`: the array size and the target sum.

The second line has `n` integers `a1, a2, ..., an`: the array values.

### Output

Print three integers: the positions of the values. If there are several solutions, you may print any of them. If there are no solutions, print `IMPOSSIBLE`.

### Constraints

- `1 <= n <= 5000`
- `1 <= x, ai <= 1e9`

### Examples

**Input**
```
4 8
2 7 5 1
```

**Output**
```
1 3 4
```

---

## What this problem really is

Find indices `i, j, k` all different with `a[i]+a[j]+a[k] = x`. `n <= 5000` allows `O(n^2)`.

## What to use

| Tool | Why |
|------|-----|
| Sort pairs `{value, index}` | Enable two pointers for the remaining sum |
| Fix one index, two-sum the rest | Outer `O(n)` × inner `O(n)` = `O(n^2)` |
| Keep original indices | Output positions, not values |

## Approach

**Root idea:** sort the array (with indices). For each fixed left position `i`, run two pointers on `(i+1 .. n-1)` seeking sum `x - a[i]`.

**Mental picture:** lock one number, then the usual “small end + big end” hunt for the partner pair that hits the leftover target.

**Data structures**

- Vector of `{value, index}` sorted by value.
- Pointers `L, R` for each fixed `i`.

**Steps**

1. Build and sort `p[0..n)` by value.
2. For `i = 0..n-3`:
   - `need = x - p[i].value`
   - `L = i+1`, `R = n-1`
   - While `L < R`: let `s = p[L].value + p[R].value`
     - if `s == need`: print the three original indices (any order)
     - if `s < need`: `L++`
     - else: `R--`
3. If none: `IMPOSSIBLE`.

## Walkthrough (on a sample)

`x=8`, values `2,7,5,1` with indices `1..4`. Sorted: `(1,4),(2,1),(5,3),(7,2)`.

Fix `1` (idx 4): need `7` → pair `2+5` → indices **4,1,3** (same as sample `1 3 4`).

## Pitfalls

- Reusing the same index twice after sorting.
- `n=5000` → `O(n^3)` TLE; need `O(n^2)`.
- Overflow: `ai` and `x` fit in `int`/`long long` sums of three — use `long long` for safety on `need`.

## Complexity

- Time: `O(n^2)`  
- Space: `O(n)`

## Key takeaway

**3-sum with positions = sort + fix one + two pointers on the rest** (`O(n^2)`, fine for `n=5000`).
