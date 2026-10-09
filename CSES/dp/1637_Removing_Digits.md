# [1637 — Removing Digits](https://cses.fi/problemset/task/1637)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, digits, shortest path  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an integer `n`. On each step, you may subtract one of the digits from the number.

How many steps are required to make the number equal to `0`?

### Input

The only input line has an integer `n`.

### Output

Print one integer: the minimum number of steps.

### Constraints

- `1 <= n <= 1e6`

### Examples

**Input**
```
27
```

**Output**
```
5
```

### Note

An optimal solution is `27 → 20 → 18 → 10 → 9 → 0`.

---

## What this problem really is

From current value `v`, you may jump to `v - d` for any digit `d` that appears in the decimal writing of `v` (`d != 0` in practice when subtracting; `0` never helps). Find the **minimum jumps** from `n` down to `0`.

## What to use

| Tool | Why |
|------|-----|
| 1D min DP | `dp[v]` = min steps from `v` to `0` |
| Digit extraction | The allowed moves depend on the digits of `v` itself |
| Bottom-up `0 .. n` | Each move goes to a strictly smaller non-negative integer |

## Approach

**Root idea:** the allowed moves from `v` are completely determined by its digits. So min steps from `v` = `1 +` min steps from each legal next value `v - d`.

Picture a number line from `0` to `n`. From each point `v` you have a few backward edges (one per nonzero digit). You want the shortest path from `n` to `0`.

```
dp[0] = 0
dp[v] = 1 + min over digits d of v (d >= 1):  dp[v - d]
```

**Steps:**

1. `dp[0] = 0`.
2. For `v = 1 .. n`:
   - Extract all digits of `v`.
   - Set `dp[v] = INF`, then for each digit `d` with `d >= 1`, set `dp[v] = min(dp[v], dp[v - d] + 1)`.
3. Answer `dp[n]`.

(You can also think “from smaller to larger”: once `dp[0..v-1]` are known, `v` only depends on smaller indices.)

## Walkthrough (on a sample)

`n = 27`. Official path: `27 → 20 → 18 → 10 → 9 → 0` (5 steps).

| `v` | Digits | Best move | `dp[v]` |
|-----|--------|-----------|---------|
| 9 | 9 | `9 → 0` | 1 |
| 10 | 1, 0 | `10 → 9` | `1 + dp[9] = 2` |
| 18 | 1, 8 | `18 → 10` | `1 + dp[10] = 3` |
| 20 | 2, 0 | `20 → 18` | `1 + dp[18] = 4` |
| 27 | 2, 7 | `27 → 20` | `1 + dp[20] = 5` |

Other branches exist (e.g. from `27` subtract `2` → `25`), but DP keeps the minimum automatically.

## Pitfalls

- **Digit `0`:** subtracting `0` does nothing — skip it.
- **Recomputing digits every state** is fine for `n = 1e6`; don’t over-optimize early.
- **Greedy “always subtract largest digit”** actually works on this problem, but the DP view transfers to other digit-move graphs; learn the DP pattern here.
- **`n = 1e6`:** `O(n * number_of_digits)` ≈ `O(n * 7)` is safe.

## Complexity

- Time: `O(n * log10(n))` digit scans  
- Space: `O(n)`

## Key takeaway

**State = current number; transition = subtract one of its digits.** Min steps = classic shortest-path DP on a DAG of decreasing integers.
