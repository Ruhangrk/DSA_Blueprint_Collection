# [1746 — Array Description](https://cses.fi/problemset/task/1746)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, counting  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You know that an array has `n` integers between `1` and `m`, and the absolute difference between two adjacent values is at most `1`.

Given a description of the array where some values may be unknown, your task is to count the number of arrays that match the description.

### Input

The first input line has two integers `n` and `m`: the array size and the upper bound for each value.

The next line has `n` integers `x1, x2, ..., xn`: the contents of the array. Value `0` denotes an unknown value.

### Output

Print one integer: the number of arrays modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 100`
- `0 <= xi <= m`

### Examples

**Input**
```
3 5
2 0 2
```

**Output**
```
3
```

### Note

The arrays `[2,1,2]`, `[2,2,2]` and `[2,3,2]` match the description.

---

## What this problem really is

Fill positions `1..n` with values in `1..m`. Fixed cells (`xi != 0`) must keep that value. Adjacent cells must satisfy `abs(a[i] - a[i+1]) <= 1`. Count valid fillings modulo `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| `dp[n+1][m+1]` (or two rows) | `dp[i][v]` = ways to fill first `i` positions ending with value `v` |
| Adjacent transition | From position `i-1` value `u`, can go to `v` only if `abs(u-v) <= 1` |
| Modulo `1e9+7` | Counts can be huge |
| `m <= 100`, `n <= 1e5` | `O(n * m)` is fine; `O(n * m^2)` also usually OK |

## Approach

**Root idea:** walk the array left to right. The only thing you need from the past is **what value the previous cell got** — that decides which values are legal now.

**Mental picture:** at index `i` you stand on a number line `1..m`. From the previous number `u`, you may step to `u-1`, `u`, or `u+1` (inside `[1,m]`). If `x[i]` is fixed, you may only land on that one point (and only if the step is legal).

**Data structures**

- Input array `x[1..n]` (`0` = free).
- `dp[i][v]` for `v = 1..m`.
- Often keep only `prev[m+1]` and `cur[m+1]` to save memory.

**Transitions**

1. **Base (`i = 1`):**
   - If `x[1] != 0`: `dp[1][x[1]] = 1` (only if `1 <= x[1] <= m`).
   - If `x[1] == 0`: `dp[1][v] = 1` for every `v` in `1..m`.

2. **For `i = 2 .. n`, for each possible `v`:**
   - If `x[i] != 0` and `v != x[i]`, skip (`dp[i][v] = 0`).
   - Else:
     ```
     dp[i][v] = 0
     for u in {v-1, v, v+1}:
         if 1 <= u <= m:
             dp[i][v] += dp[i-1][u]
     dp[i][v] %= MOD
     ```

3. **Answer:** sum of `dp[n][v]` over `v = 1..m`, modulo `MOD`.

## Walkthrough (on a sample)

`n = 3`, `m = 5`, array `2, 0, 2`.

**i = 1:** fixed `2` → `dp[1][2] = 1`, other `dp[1][*] = 0`.

**i = 2:** free. Possible `v`:
- `v = 1`: from `u` in `{1,2}` → only `dp[1][2]` → `1`
- `v = 2`: from `{1,2,3}` → `1`
- `v = 3`: from `{2,3,4}` → `1`
- `v = 4,5`: need `dp[1][3]` etc. → `0`

So middle can be `1`, `2`, or `3` (one way each so far).

**i = 3:** fixed `2`. Only `v = 2`, from previous `u` in `{1,2,3}`:
`dp[2][1] + dp[2][2] + dp[2][3] = 1+1+1 = 3`.

Arrays: `[2,1,2]`, `[2,2,2]`, `[2,3,2]` → **3**.

## Pitfalls

- **`x[i]` outside `1..m` when nonzero** — treat as impossible (0 ways) if it ever appears.
- **Forgetting bounds** on `u = v-1` / `v+1`.
- **`n = 1e5`** — must be linear in `n` times small `m`; never exponential backtracking.
- **Modulo** on every addition.
- Fixed cells that disagree with neighbors (e.g. `1 0 5` with `m` large but middle cannot bridge) correctly yield `0` via DP.

## Complexity

- Time: `O(n * m)`  
- Space: `O(m)` with two rows, or `O(n * m)` full table

## Key takeaway

**Adjacent constraint + fill left to right → DP on (position, last value).** Unknowns try all legal neighbors of the previous value; fixed cells just restrict which `v` are allowed.
