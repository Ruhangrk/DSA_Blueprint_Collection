# [3302 — Subsets with Fixed Average](https://cses.fi/problemset/task/3302)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Subset counting, DP over sums, shifting values, offset arrays  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an array of `n` integers. Count the non-empty subsets whose average equals `a`, modulo `1e9 + 7`.

### Input

- The first line has `n` and `a`.
- The next line has `x[1], ..., x[n]`.

### Output

Print one integer: the count modulo `1e9 + 7`.

### Constraints

- `1 <= n <= 500`
- `1 <= a <= 500`
- `1 <= x[i] <= 500`

### Examples

**Input**
```
5 2
1 1 2 3 4
```

**Output**
```
7
```

---

## What this problem really is

A subset has average `a` exactly when the sum of `(x[i] - a)` over its elements is zero. So after subtracting `a` from every value, we count the subsets whose shifted values add up to zero.

## What to use

| Tool | Why |
|------|-----|
| Shift by `a`: `y[i] = x[i] - a` | Turns "average equals `a`" into "sum equals 0" |
| Elements with `y = 0` | They never change the sum, so each one doubles the count |
| Subset-sum DP with an offset | Counts subsets for every possible sum in a fixed range |
| Subtract 1 at the end | Removes the empty subset, which is not allowed |

## Approach

**Root idea:** average `a` means the shifted sum is `0`. Items with `y = 0` can be included or left out freely, so they multiply the count by `2` each. Every other item is a step of size `y` on a number line, and we count the ways to end at `0`.

**Mental picture:** start at position `0` on a number line. Each item with a positive `y` is a step right, and each negative `y` is a step left. A subset is a choice of which steps to take, and we want the choices that return to `0`. Keep a table that says, for each position, how many choices land there. Processing an item means: every position that was reachable gets a new copy one step further along.

**How that becomes the method**

1. Compute `y[i] = x[i] - a`. Let `z` be the number of `y[i] = 0`.
2. Let `S = sum of |y[i]|` over the nonzero `y[i]`. Positions run from `-S` to `S`, stored with offset `S`.
3. `ways[s]` = number of subsets of the processed nonzero items with sum `s`. Start with `ways[0] = 1`.
4. For each nonzero `y`, add the shifted table into itself. Iterate in the direction that reads old values first, so each item is used at most once.
5. Let `base = ways[0]`: the number of nonzero subsets with sum `0`, including the empty one.
6. Answer = `base * 2^z - 1`, modulo `1e9 + 7`.

**Data structures:**
- `ways[]` of size `2S + 1`, indexed by `sum + S`.
- A running power of two for `2^z`.

## Walkthrough (on a sample)

`n = 5`, `a = 2`, `x = 1 1 2 3 4`. Shifted values: `y = -1, -1, 0, 1, 2`. So `z = 1` (the `2`), and the nonzero items are `-1, -1, 1, 2`.

Each row shows the table after processing one item. Only nonzero entries are listed.

| Item | Table after the item |
|---|---|
| start | `{0: 1}` |
| `-1` | `{0: 1, -1: 1}` |
| `-1` | `{0: 1, -1: 2, -2: 1}` |
| `+1` | `{0: 3, -1: 3, -2: 1, +1: 1}` |
| `+2` | `{0: 4, -1: 3, -2: 1, +1: 4, +2: 3, +3: 1}` |

How the last two rows are built:
- For `+1`: the old table is `{0:1, -1:2, -2:1}`. Shifting it by `+1` gives `{1:1, 0:2, -1:1}`. Add the two tables: `0: 1+2 = 3`, `-1: 2+1 = 3`, `-2: 1`, `+1: 1`.
- For `+2`: the table is `{0:3, -1:3, -2:1, +1:1}`. Shifting it by `+2` gives `{2:3, 1:3, 0:1, +3:1}`. Add: `0: 3+1 = 4`, `+1: 1+3 = 4`, `+2: 3`, `+3: 1`, `-1: 3`, `-2: 1`.

So `base = ways[0] = 4`. The nonzero zero-sum subsets are `{}`, `{-1a, +1}`, `{-1b, +1}`, and `{-1a, -1b, +2}`.

The zero-valued element `2` doubles this: `4 * 2 = 8` subsets, including the empty one.

Answer = `8 - 1 = 7`. Matches.

Check by listing the 7 subsets with average `2`: `{2}`, `{1, 3}` (two choices of the `1`), `{1, 1, 4}`, `{2, 1, 3}` (two choices of the `1`), and `{2, 1, 1, 4}`.

## Pitfalls

- Forgetting to multiply by `2^z`. Elements equal to `a` are valid alone and must be counted.
- Forgetting to subtract the empty subset. It has sum `0`, so it is counted, but it is not a valid answer.
- Updating the table in the wrong direction. Then one item can be used twice.
- Range too small. Sums can reach `+-S`, so the table must cover the full range.
- Shifting the wrong way. It is `x - a`, applied to every element.

## Complexity

- Time: `O(n * S)` with `S <= n * 500 = 2.5e5`. About `2.5e8` simple operations in the worst case, which fits in 1 second with a tight loop.
- Space: `O(S)`.

## Key takeaway

An average equal to `a` is a zero-sum condition after subtracting `a` from each value. Count zero-sum subsets with a sum DP, and treat zero-valued items as free doublings.
