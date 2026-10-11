# [1159 — Book Shop II](https://cses.fi/problemset/task/1159)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems II  
**Tags:** Bounded knapsack, binary splitting, 0/1 knapsack DP  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A shop sells `n` kinds of books. Book `i` has price `h[i]`, `s[i]` pages, and `k[i]` copies. With total price at most `x`, maximize the total number of pages you can buy. You may buy several copies of the same book, up to `k[i]`.

### Input

- The first line has `n` and `x`.
- The next line has `h[1..n]`.
- The next line has `s[1..n]`.
- The last line has `k[1..n]`.

### Output

Print the maximum number of pages.

### Constraints

- `1 <= n <= 100`
- `1 <= x <= 1e5`
- `1 <= h[i], s[i], k[i] <= 1000`

### Examples

**Input**
```
3 10
2 6 3
8 5 4
3 5 2
```

**Output**
```
28
```

**Explanation:** Buy three copies of book 1 and one copy of book 3. Price `3 * 2 + 3 = 9`, pages `3 * 8 + 4 = 28`.

---

## What this problem really is

This is a 0/1 knapsack where each book can be taken up to `k[i]` times. Each copy has a weight (price) and a value (pages). Maximize value under a weight limit. The trick is to avoid expanding every copy: group copies into a few bundles.

## What to use

| Tool | Why |
|------|-----|
| Binary splitting of copies | Any count from `0` to `k` can be built from bundles of sizes `1, 2, 4, ...` plus a remainder |
| 0/1 knapsack over bundles | Each bundle is taken or not, so the standard reverse-order update works |
| 1D DP array of size `x + 1` | Stores the best pages for each total price |

## Approach

**Root idea:** for book `i` with `k` copies, split the copies into bundles of sizes `1, 2, 4, ..., 2^(t-1)` plus one more bundle with the remaining count. Any number of copies from `0` to `k` can be formed by choosing a subset of these bundles, because the power-of-two bundles cover every integer below their total. So each bundle becomes a single 0/1 item with weight `bundle * h[i]` and value `bundle * s[i]`.

**Mental picture:** imagine a shelf with `k` identical books. Instead of handling them one by one, you pack them into boxes of 1, 2, 4, and so on. You can pick any combination of boxes to get any number of books. The boxes are then just ordinary items for a standard knapsack.

**How that becomes the method**

1. For each book, split `k[i]` into bundles: sizes `1, 2, 4, ...` while the running total is at most `k[i]`, then one bundle with the remainder.
2. Each bundle `b` gives an item with weight `b * h[i]` and value `b * s[i]`.
3. Run 0/1 knapsack: `dp[w]` = maximum pages with total price at most `w`, initialized to `0`. For each item, for `w` from `x` down to the item's weight, `dp[w] = max(dp[w], dp[w - weight] + value)`.
4. Output `dp[x]`.

Going downward in `w` makes sure each bundle is used at most once.

**Data structures:** a list of bundle items, and a `dp` array of size `x + 1`.

## Walkthrough (on a sample)

`n = 3`, `x = 10`.

Books:
- Book 1: price `2`, pages `8`, copies `3`. Bundles: `1` (sizes `1`) and `2` (size `2`). Items: `(weight 2, value 8)` and `(weight 4, value 16)`.
- Book 2: price `6`, pages `5`, copies `5`. Bundles: `1`, `4`. Items: `(6, 5)` and `(24, 20)`. The second exceeds `x`, so it is ignored.
- Book 3: price `3`, pages `4`, copies `2`. Bundles: `1`, `1`. Items: `(3, 4)` and `(3, 4)`.

Items with weight at most `10`: `(2, 8)`, `(4, 16)`, `(6, 5)`, `(3, 4)`, `(3, 4)`.

Name the items: `A = (2, 8)`, `B = (4, 16)`, `C = (6, 5)`, `D = (3, 4)`, `E = (3, 4)`. Each is used at most once. Find the best subset with total weight at most `10`:

- `A + B + D` has weight `2 + 4 + 3 = 9` and value `8 + 16 + 4 = 28`.
- `A + B + E` gives the same `28`.
- `B + D + E` has weight `10` and value `24`.
- `A + B + D + E` has weight `12`, too heavy.
- Any subset using `C` has value at most `21` (for example `C + B` with weight `10`, value `21`), so it cannot beat `28`.

The DP finds the same best value: after the items are processed in turn, `dp[10]` ends at `28`. Matches the expected output.

The chosen set `A + B + D` is book 1 with bundles `1` and `2` (three copies, weight `6`, value `24`) plus one copy of book 3 (weight `3`, value `4`), total weight `9` and value `28`.

## Pitfalls

- Expanding every copy into its own item. That is still fine here, but binary splitting keeps the item count small.
- Updating `dp` upward in `w`. That lets one bundle be used many times; iterate downward.
- Forgetting that a bundle heavier than `x` can be dropped.
- Using `dp[w]` as "exactly price `w`". Initialize all `dp[w] = 0` so that it means "at most `w`".
- Forgetting the remainder bundle. With `k = 5`, the bundles are `1, 2, 2` (sum `5`), not only `1, 2, 4`.

## Complexity

- Time: `O(x * (total bundles))`. Total bundles is at most about `n * log(max k) = 100 * 10`, so about `1e5 * 1000 = 1e8` simple operations.
- Space: `O(x)`.

## Key takeaway

A bounded knapsack becomes a 0/1 knapsack by grouping copies into power-of-two bundles plus a remainder. Any count is then a subset of bundles.
