# [1653 — Elevator Rides](https://cses.fi/problemset/task/1653)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, bitmasks, subset DP  
**Difficulty / Rating:** Advanced (bitmask DP)  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` people who want to get to the top of a building which has only one elevator. You know the weight of each person and the maximum allowed weight in the elevator. What is the minimum number of elevator rides?

### Input

The first input line has two integers `n` and `x`: the number of people and the maximum allowed weight in the elevator.

The second line has `n` integers `w1, w2, ..., wn`: the weight of each person.

### Output

Print one integer: the minimum number of rides.

### Constraints

- `1 <= n <= 20`
- `1 <= x <= 1e9`
- `1 <= wi <= x`

### Examples

**Input**
```
4 10
4 8 6 1
```

**Output**
```
2
```

---

## What this problem really is

Partition `n` people into the **fewest groups** such that each group’s total weight is `<= x`. Order inside a ride does not matter; rides are identical except for who is in them.

## What to use

| Tool | Why |
|------|-----|
| Bitmask | `n <= 20` → every subset of people fits in an `int` mask (`2^n` states) |
| Subset DP | Optimal packing for a set of people builds from smaller sets |
| Pair state | Need both “rides so far” and “how full the current ride is” |

## Approach

**Root idea:** while people are still waiting, you are always filling **one current ride**. For any subset `S` of people already sent up, remember:

1. the fewest rides used to move everyone in `S`, and  
2. how much weight is already on the **last** of those rides.

Then, when you add a new person, either they **fit in that last ride**, or they **force a brand-new ride**.

Picture `dp[mask]` as a pair `(rides, last_load)` for the people whose bits are set in `mask`:

- `rides` = minimum elevator trips to transport exactly those people  
- `last_load` = among all ways that achieve that minimum `rides`, the **smallest** weight sitting in the final trip (leaves the most free space for later people)

**Transition:** for each `mask`, for each person `i` not in `mask`, form `nmask = mask | (1 << i)`:

- If `last_load + w[i] <= x` → person joins the current ride:  
  candidate = `(rides, last_load + w[i])`
- Else → open a new ride that starts with only `i`:  
  candidate = `(rides + 1, w[i])`

Keep the lexicographically better pair for `nmask` (fewer rides first; if tied, smaller `last_load`).

**Base:** `dp[0] = (1, 0)` — think of an empty “open” ride with load `0` so the first person always starts that ride (or equivalently `(0, 0)` and treat the first add carefully; the `(1, 0)` base is the usual clean version).

**Answer:** `dp[(1 << n) - 1].rides`.

## Walkthrough (on a sample)

`n = 4`, `x = 10`, weights `4, 8, 6, 1` (people `0..3`).

One optimal split: ride `{8, 1}` (weight `9`) and ride `{4, 6}` (weight `10`) → **2** rides.

How the DP sees it (sketch):

- Empty → `(rides=1, last=0)`.
- Add `8`: does not fit in load `0` as “extra” beyond opening — with base `(1,0)`, `0+8 <= 10` → `(1, 8)`.
- Add `1` onto that: `8+1 <= 10` → `(1, 9)`. Mask = `{8,1}`.
- Add `4` onto `{8,1}`: `9+4 > 10` → new ride `(2, 4)`.
- Add `6`: `4+6 <= 10` → `(2, 10)`. Full set done in **2** rides.

Greedy “heaviest first” can also hit 2 here; the DP is what guarantees the minimum for every weight set when `n` is tiny.

## Pitfalls

- **Only minimizing rides** without tracking `last_load` is not enough — two plans with the same ride count can leave different free space for the next person.
- **`n = 20`** → `O(n * 2^n)` is intended; do not try `n!` pairings.
- **Weights up to `1e9`, `x` up to `1e9`** — use 64-bit for loads; never allocate an array of size `x`.
- **Every `wi <= x`** is given — a single person always fits alone.

## Complexity

- Time: `O(n * 2^n)`  
- Space: `O(2^n)` for the DP pairs

## Key takeaway

**Bitmask + “state of the open bin.”** When packing items into fewest capacity-`x` bins and `n` is tiny, DP over subsets storing how full the current bin is.
