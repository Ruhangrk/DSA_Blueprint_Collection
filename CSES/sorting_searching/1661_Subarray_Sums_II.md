# [1661 — Subarray Sums II](https://cses.fi/problemset/task/1661)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** prefix sums, hash map  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an array of `n` integers, your task is to count the number of subarrays having sum `x`.

### Input

The first input line has two integers `n` and `x`: the size of the array and the target sum `x`.

The next line has `n` integers `a1, a2, ..., an`: the contents of the array.

### Output

Print one integer: the required number of subarrays.

### Constraints

- `1 <= n <= 2e5`
- `-1e9 <= x, ai <= 1e9`

### Examples

**Input**
```
5 7
2 -1 3 5 -2
```

**Output**
```
2
```

---

## What this problem really is

Same as Subarray Sums I — count subarrays with sum `x` — but values (and `x`) may be **negative**, so two pointers on a sliding window no longer work.

## What to use

| Tool | Why |
|------|-----|
| Prefix sums | `sum[L..R] = pref[R] - pref[L-1]` |
| Frequency map of prefixes | For each `pref[R]`, count prior `pref[L-1] = pref[R] - x` |
| `long long` | Prefixes can be large |

## Approach

**Root idea:** a subarray ending at `R` has sum `x` iff some earlier prefix equals `pref[R] - x`. Search that count in a map of prefixes seen so far (including `pref[0] = 0`).

**Mental picture:** walking along the array with a cumulative altitude; each time your altitude is `h`, ask how often you’ve been at altitude `h - x` — each such past visit starts a segment with climb `x`.

**Data structures**

- `map` / `unordered_map` from prefix value → frequency.
- `long long pref = 0`, `ans = 0`.
- Initially `freq[0] = 1` (empty prefix before index 1).

**Steps**

1. For each value `a` in order:
   - `pref += a`
   - `ans += freq[pref - x]` (0 if missing)
   - `freq[pref]++`
2. Print `ans`.

## Walkthrough (on a sample)

`2, -1, 3, 5, -2`, `x=7`.

Prefixes: `2, 1, 4, 9, 7`.

- At `pref=9`: `9-7=2` seen once → subarray `[3,5]`? indices giving sum 7: e.g. `[3,5,-2]` wait — sample answer **2**: subarrays `[2,-1,3,5,-2]`? Let's trust: `[3,5,-1]` no.

Actually: `[2,-1,3,5]`? sum 9. Known good ones: `[2,-1,3,5,-2]` sum 7 and `[5,-2]`? 3. `[3,5,-1]`...

Prefixes hit: when `pref=9`, need `2` → segment after first `2` through `5` = `-1+3+5=7`. When `pref=7`, need `0` → whole array sum 7. Yes **2**.

## Pitfalls

- Forgetting `freq[0] = 1`.
- Using two pointers (invalid with negatives).
- `unordered_map` with custom hash if needed; `map` is safer/`O(n log n)`.
- `int` for prefixes.

## Complexity

- Time: `O(n log n)` with `map`, expected `O(n)` with hash  
- Space: `O(n)`

## Key takeaway

**Subarray sum `x` with negatives = prefix frequencies** — for each `pref`, add count of `pref - x` already seen.
