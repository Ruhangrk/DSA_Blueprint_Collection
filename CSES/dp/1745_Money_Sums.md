# [1745 — Money Sums](https://cses.fi/problemset/task/1745)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, subset sum  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You have `n` coins with certain values. Your task is to find all money sums you can create using these coins.

### Input

The first input line has an integer `n`: the number of coins.

The next line has `n` integers `x1, x2, ..., xn`: the values of the coins.

### Output

First print an integer `k`: the number of distinct money sums. After this, print all possible sums in increasing order.

### Constraints

- `1 <= n <= 100`
- `1 <= xi <= 1000`

### Examples

**Input**
```
4
4 2 5 2
```

**Output**
```
9
2 4 5 6 7 8 9 11 13
```

---

## What this problem really is

Each coin used **at most once** (they are distinct coins, even if values repeat). List every achievable subset sum, sorted, and how many there are. Sum `0` is usually **not** printed (empty selection) — the sample starts at `2`.

## What to use

| Tool | Why |
|------|-----|
| Boolean DP `can[0..S]` | `can[s] = true` if some subset sums to `s` |
| Total sum `S` | Upper bound on any subset sum; `S <= 100 * 1000 = 1e5` |
| Backward update | Same 0/1 rule: each coin applied once |

## Approach

**Root idea:** a sum `s` is possible iff for some coin `x` you already could make `s - x` **without** that coin. Process coins one by one and mark new sums.

**Mental picture:** a row of light bulbs `0 .. S`. Bulb `0` starts on. Each coin flips “on” every bulb that is exactly `x` steps to the right of an already-on bulb (without reusing the coin).

**Data structures**

- `x[n]` coin values.
- `S = sum of all x[i]`.
- `vector<char> can(S+1, false)` or `bool` array; set `can[0] = true`.

**Update (per coin `x`)**

```
for s = S; s >= x; s--:
    if (can[s - x]) can[s] = true
```

**Steps**

1. Read coins, compute `S`, init `can[0] = true`.
2. For each coin, run the backward loop above.
3. Collect every `s >= 1` with `can[s]`.
4. Print count, then the sums in increasing order.

## Walkthrough (on a sample)

Coins `4, 2, 5, 2`. `S = 13`.

After all updates, achievable positive sums:

`2, 4, 5, 6 (4+2), 7 (5+2), 8 (4+2+2), 9 (4+5), 11 (4+5+2), 13 (all)` → **9** sums.

(`1` and `3` never light up — no subset makes them.)

## Pitfalls

- **Forward loop** → using the same coin repeatedly (wrong model here).
- **Printing `0`** — sample does not; only positive achievable sums.
- **Duplicate values** (two coins of `2`) — they are still two separate coins; 0/1 DP handles that naturally as two items.
- Forgetting to scan `s` only up to current reachable max is optional optimization; full `S` is fine.

## Complexity

- Time: `O(n * S)` with `S <= 1e5`  
- Space: `O(S)`

## Key takeaway

**All subset sums = boolean 0/1 knapsack on sum.** Backward updates per coin; then list every `true` index.
