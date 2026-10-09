# [1690 — Hamiltonian Flights](https://cses.fi/problemset/task/1690)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, DP on subsets, Hamiltonian paths  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `m` flight connections between them. You want to travel from Syrjälä to Lehmälä so that you visit each city exactly once. How many possible routes are there?

### Input

The first input line has two integers `n` and `m`: the number of cities and flights. The cities are numbered `1, 2, ..., n`. City `1` is Syrjälä, and city `n` is Lehmälä.

Then, there are `m` lines describing the flights. Each line has two integers `a` and `b`: there is a flight from city `a` to city `b`. All flights are one-way flights.

### Output

Print one integer: the number of routes modulo `1e9+7`.

### Constraints

- `2 <= n <= 20`
- `1 <= m <= n^2`
- `1 <= a, b <= n`

### Examples

**Input**
```
4 6
1 2
1 3
2 3
3 2
2 4
3 4
```

**Output**
```
2
```

---

## What this problem really is

Count Hamiltonian paths from `1` to `n` in a digraph (visit every city exactly once). `n <= 20` → subset DP.

## What to use

| Tool | Why |
|------|-----|
| `dp[mask][v]` | Ways to visit exactly the set `mask`, ending at `v` |
| Bitmask of cities | `2^n` sets |
| Adj matrix / list | Transitions along flights |
| Modulo `1e9+7` | Count routes |

## Approach

**Root idea:** a partial route is described by “which cities used” and “where you are now.” Extend by flying to an unused city.

**Mental picture:** fill a subset of the map without repeats; only complete masks that end at `n` and include everyone count.

**Data structures**

- Cities `0..n-1` internally (`1→0`, `n→n-1`) or keep 1-based with bit `(1<<(i-1))`.
- `dp[1<<n][n]` (or rolling), init `dp[1<<(start)][start] = 1`.

**Recurrence**

```
for mask in 0 .. (1<<n)-1:
  for v in mask:
    for each flight v → u:
      if u not in mask:
        dp[mask|(1<<u)][u] += dp[mask][v]  (mod)
```

**Answer:** `dp[(1<<n)-1][n-1]` (end city), only if start is city `0` / `1`.

Require start bit set in every considered path (natural if you only grow from the init state).

**Steps**

1. Build digraph.
2. Init DP at city `1` alone.
3. Enumerate masks and relax flights to new cities.
4. Print ways for full mask ending at `n`.

## Walkthrough (on a sample)

`n=4`: two Ham paths `1→2→3→4` and `1→3→2→4` → **`2`**.

## Pitfalls

- Allowing revisits — must check `u not in mask`.
- Ending at wrong city.
- `O(2^n n^2)` must stay within `n=20` limits; tight code helps.

## Complexity

- Time: `O(2^n * n^2)` worst with adj matrix scans; better `O(2^n * (n+m))` with adj lists over `v in mask`  
- Space: `O(2^n * n)`

## Key takeaway

**Count Ham paths with small `n`:** subset DP `dp[mask][end]` = ways to permute `mask` ending at `end`, start fixed at `1`, finish at `n`.
