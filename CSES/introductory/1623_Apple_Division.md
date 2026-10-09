# [1623 — Apple Division](https://cses.fi/problemset/task/1623)

**Platform:** CSES  
**Source:** CSES Problem Set — Introductory Problems  
**Tags:** meet in the middle, bitmasks, partition  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` apples with known weights. Your task is to divide the apples into two groups so that the difference between the weights of the groups is minimal.

### Input

The first input line has an integer `n`: the number of apples.

The next line has `n` integers `p1, p2, ..., pn`: the weight of each apple.

### Output

Print one integer: the minimum difference between the weights of the groups.

### Constraints

- `1 <= n <= 20`
- `1 <= pi <= 1e9`

### Examples

**Input**
```
5
3 2 7 4 1
```

**Output**
```
1
```

Explanation: Group 1 has weights `2, 3` and `4` (total weight `9`), and group 2 has weights `1` and `7` (total weight `8`).

---

## What this problem really is

Partition into two subsets minimizing `|2*sum(A) - total|` (empty group allowed). `n <= 20` → enumerate subsets.

## What to use

| Tool | Why |
|------|-----|
| Bitmask `0 .. (1<<n)-1` | Each bit = apple in group A |
| Or recursion / meet-in-the-middle | Same idea; MITM optional at `n=20` |
| `long long` | Sums up to `20 * 1e9` |

## Approach

**Root idea:** for every subset, let `s` be its weight sum. Difference = `|total - 2*s|`. Track the minimum over all subsets (each partition counted twice — fine).

**Mental picture:** for each apple, put it left or right; score how unbalanced the scales are; keep the best.

**Steps**

1. `total = sum(p)`.
2. `best = total`.
3. For `mask = 0 .. (1<<n)-1`: compute subset sum `s`; `best = min(best, abs(total - 2*s))`.
4. Print `best`.

(Recursion assigning each apple to A or B is equivalent and uses less constant factor.)

## Walkthrough (on a sample)

`total=17`. Subset `{2,3,4}` sum `9` → `|17-18|=1` → **1**.

## Pitfalls

- Using `int` for sums.
- Thinking groups must be nonempty — empty is OK (difference = total).
- `O(n * 2^n)` is fine; `O(3^n)` unnecessary.

## Complexity

- Time: `O(n * 2^n)`  
- Space: `O(n)`

## Key takeaway

**Min partition difference = try all subsets**; answer `min |total - 2*subset_sum|`.
