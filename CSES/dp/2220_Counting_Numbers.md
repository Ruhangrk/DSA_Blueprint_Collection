# [2220 — Counting Numbers](https://cses.fi/problemset/task/2220)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, digit DP  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to count the number of integers between `a` and `b` where no two adjacent digits are the same.

### Input

The only input line has two integers `a` and `b`.

### Output

Print one integer: the answer to the problem.

### Constraints

- `0 <= a <= b <= 1e18`

### Examples

**Input**
```
123 321
```

**Output**
```
171
```

---

## What this problem really is

Count integers `x` with `a <= x <= b` whose decimal representation has **no two consecutive equal digits**. Range up to `1e18` → cannot iterate; use digit DP. Include `0` correctly.

## What to use

| Tool | Why |
|------|-----|
| Digit DP `f(R)` | Count valid numbers in `[0, R]`, then answer `f(b) - f(a-1)` |
| State `(pos, prev_digit, tight, started)` | Standard digits-from-left construction |
| Memoization | Digits of `1e18` → at most ~19 positions |

## Approach

**Root idea:** build the number from the most significant digit to the least. At each position choose a digit `d` different from the previous one (once the number has started). Respect the upper bound while `tight` is true.

**Mental picture:** walk along the decimal digits of the limit. “Tight” means you still match the limit’s prefix; after placing a smaller digit, the rest are free (`0..9` with the adjacency rule).

**Data structures**

- Digits of the upper bound in a vector `dig` (MSB first).
- Memo array on `(pos, prev, tight, started)` → `long long`.

**`count_upto(R)`** — numbers in `0..R` with no adjacent equal digits:

1. If `R < 0` return `0` (for `a = 0` handle `f(a-1)` carefully).
2. Write `R`’s digits.
3. DFS/DP from `pos = 0`, `prev = 0` (unused), `tight = 1`, `started = 0`:
   - If `pos == len`: return `1` (one completed number; leading-zero path counts as `0`).
   - `limit = tight ? dig[pos] : 9`.
   - For `d = 0 .. limit`:
     - `n_started = started || (d != 0)`.
     - If `started` and `d == prev`: skip.
     - Recurse with `n_prev = n_started ? d : 0`, `n_tight = tight && (d == limit)`.
4. Memoize and return.

**Answer:** `count_upto(b) - count_upto(a - 1)` (with `count_upto(-1) = 0`).

**Steps**

1. Implement `count_upto`.
2. Read `a`, `b`.
3. Print the difference.

## Walkthrough (on a sample)

`a = 123`, `b = 321` → **`171`** valid integers in that range.

Spot-check idea: `110` is invalid (two `1`s); `121` is valid; `321` valid. Digit DP counts all such without listing.

For a tiny bound, e.g. `R = 11`: valid among `0..11` include `0..10` but not `11` → the DP’s tight path rejects the second `1` when `prev = 1` and `started = 1`.

## Pitfalls

- **`a = 0`** — `a-1` is `-1`; define `count_upto(-1) = 0`.
- **Leading zeros** — don’t enforce adjacency until `started` becomes true (so `0` is valid; `101` is fine).
- **`prev` before start** — no restriction on the first non-zero digit.
- Use `long long` for answers (up to ~`1e18` counts fit in 64-bit here).

## Complexity

- Time: `O(digits * states)` ≈ `O(19 * 10 * 2 * 2)` per `count_upto` with memo  
- Space: memo of similar size

## Key takeaway

**Range constraints on digits → digit DP.** Count to `R` with state `(position, previous digit, tight, started)`, then take `f(b) - f(a-1)`.
