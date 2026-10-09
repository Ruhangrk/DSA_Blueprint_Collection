# [2413 — Counting Towers](https://cses.fi/problemset/task/2413)

**Platform:** CSES  
**Source:** CSES Problem Set — Dynamic Programming  
**Tags:** dynamic programming, counting, tiling  
**Difficulty / Rating:** Advanced (tiling DP)  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to build a tower whose width is `2` and height is `n`. You have an unlimited supply of blocks whose width and height are integers.

For example, here are some possible solutions for `n = 6`.

Given `n`, how many different towers can you build? Mirrored and rotated towers are counted separately if they look different.

### Input

The first input line contains an integer `t`: the number of tests.

After this, there are `t` lines, and each line contains an integer `n`: the height of the tower.

### Output

For each test, print the number of towers modulo `1e9+7`.

### Constraints

- `1 <= t <= 100`
- `1 <= n <= 1e6`

### Examples

**Input**
```
3
2
6
1337
```

**Output**
```
8
2864
640403945
```

---

## What this problem really is

Count ways to tile a `2 x n` board (width `2`, height `n`) using rectangular bricks of any integer sizes. Two towers that look different after drawing brick boundaries count as different — reflections are separate if they look different. Modulo `1e9+7`. Many queries, `n` up to `1e6`.

## What to use

| Tool | Why |
|------|-----|
| Two DP sequences up to `1e6` | `t` queries share one precomputation |
| State = how the **top row** is joined | Only local layout of the last level matters for extending |
| `long long` + modulo | Counts grow fast |

## Approach

**Root idea:** do not place “any rectangle” freely in the DP. Instead, build the tower **row by row** and only remember whether the two cells of the current top row belong to the **same brick** or to **two different bricks**. That one bit of layout is enough to know how you may grow the tower upward.

**Mental picture**

```
width 2, height growing upward

  [ L | R ]   ← top row
  [ ...   ]
```

Two kinds of tops:

- **`solid[h]`** — cells `L` and `R` at height `h` are part of **one** brick (a piece that spans both columns at this row).
- **`split[h]`** — `L` and `R` belong to **different** bricks.

**Base (`h = 1`)**

- `solid[1] = 1` — one brick covering the whole `2 x 1` row  
- `split[1] = 1` — two separate `1 x 1` bricks side by side  

**Transitions** (build height `h` from height `h-1`)

```
solid[h] = 2 * solid[h-1] + split[h-1]
split[h] = solid[h-1] + 4 * split[h-1]
```

Why `solid[h]`:

- From a **solid** top: (1) extend that same wide brick one row higher, or (2) close it and put a **new** wide brick on top → factor `2`.
- From a **split** top: the only way to become solid is to place a **new** wide brick across both columns → factor `1`.

Why `split[h]`:

- From a **solid** top: place two fresh single-column bricks → factor `1`.
- From a **split** top: for **each** column independently, either extend the brick below or start a new one → `2 * 2 = 4` options.

**Answer for one `n`:** `(solid[n] + split[n]) % MOD`.

**Data structures**

- Arrays `solid[0..MAXN]`, `split[0..MAXN]` with `MAXN = 1e6`.
- Precompute once for all tests; then each query is `O(1)`.

**Steps**

1. Set `MOD = 1e9+7`, `solid[1] = split[1] = 1`.
2. For `h = 2 .. 1e6`, apply the two transitions with modulo.
3. For each test `n`, print `(solid[n] + split[n]) % MOD`.

## Walkthrough (on a sample)

Compute small heights:

| `h` | `solid[h]` | `split[h]` | total |
|-----|------------|------------|-------|
| 1 | 1 | 1 | 2 |
| 2 | `2*1+1 = 3` | `1+4*1 = 5` | **8** |
| 3 | `2*3+5 = 11` | `3+4*5 = 23` | 34 |
| … | … | … | … |

Sample first line `n = 2` → **8**, matches.

For `n = 6` the same recurrence continues to **2864**; for `n = 1337` to **640403945** (after mod). You do not recompute from scratch per query — fill once up to `1e6`.

## Pitfalls

- **Trying to DP over arbitrary brick sizes explicitly** — too many shapes; the solid/split abstraction is the point.
- **`n = 1e6`, `t = 100`** — must precompute `O(MAXN)`, not `O(t * n)` with a slow inner method.
- **Modulo** on every multiply/add (`2 * solid` can be taken mod carefully).
- **Off-by-one on base** — if `solid[1]`/`split[1]` wrong, every later value is wrong (`n=2` must be `8` as a check).

## Complexity

- Time: `O(MAXN + t)` with `MAXN = 1e6`  
- Space: `O(MAXN)`

## Key takeaway

**Tiling a fixed-width strip:** compress the top seam into a few layouts (`solid` vs `split`) and recur on height. Precompute when many queries share the same recurrence.
