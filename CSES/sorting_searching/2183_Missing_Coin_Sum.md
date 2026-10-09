# [2183 — Missing Coin Sum](https://cses.fi/problemset/task/2183)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** sorting, greedy, subset sums  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have `n` coins with positive integer values. What is the smallest sum you cannot create using a subset of the coins?

### Input

The first line has an integer `n`: the number of coins.

The second line has `n` integers `x1, x2, ..., xn`: the value of each coin.

### Output

Print one integer: the smallest coin sum.

### Constraints

- `1 <= n <= 2e5`
- `1 <= xi <= 1e9`

### Examples

**Input**
```
5
2 9 1 2 7
```

**Output**
```
6
```

---

## What this problem really is

Smallest positive integer that is **not** a subset sum of the given multiset of coin values. `n` is large, so no DP over the total sum.

## What to use

| Tool | Why |
|------|-----|
| Sort coins ascending | Process in order for the classic reach argument |
| Running `reach` | After some coins, all sums in `1..reach` are possible |
| Greedy extension | Next coin `c <= reach+1` extends reach to `reach+c` |

## Approach

**Root idea:** maintain the largest `reach` such that every integer in `1..reach` is achievable (`reach = 0` initially). Scan coins from small to large. If the next coin `c` satisfies `c > reach+1`, then `reach+1` is impossible. Otherwise you can attach `c` to every already-achievable sum and the new reachable prefix becomes `1..reach+c`.

**Mental picture:** a solid block of makeable totals starting at 1. A new coin can only glue onto the right end if it isn’t bigger than `reach+1` (no hole). If it jumps past that, the hole at `reach+1` is the answer.

**Data structures**

- Sorted array `x`.
- `long long reach = 0`.

**Steps**

1. Sort `x` ascending.
2. For each coin `c` in order:
   - if `c > reach + 1`: break / return `reach + 1`
   - else `reach += c`
3. Answer is `reach + 1` (after all coins, or at the break).

## Walkthrough (on a sample)

Sorted: `1,2,2,7,9`.

- `1`: `1 <= 0+1` → `reach = 1` (can make `1`)
- `2`: `2 <= 2` → `reach = 3` (can make `1..3`)
- `2`: `2 <= 4` → `reach = 5`
- `7`: `7 > 6` → cannot make **6** → answer **6**

(`9` never needed.)

## Pitfalls

- Forgetting to sort.
- Using `c > reach` instead of `c > reach+1`.
- DP `bool can[sum]` — impossible at these constraints.
- `int` for `reach` (can exceed `2e5 * 1e9` in theory — use `long long`).

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Smallest unreachable subset sum = sort, grow `reach`, stop at first coin `> reach+1`** — answer is `reach+1`.
