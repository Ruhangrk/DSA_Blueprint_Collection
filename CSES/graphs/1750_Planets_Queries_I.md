# [1750 — Planets Queries I](https://cses.fi/problemset/task/1750)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, functional graph, binary lifting  
**Difficulty / Rating:** Medium–Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are playing a game consisting of `n` planets. Each planet has a teleporter to another planet (or the planet itself).

Your task is to process `q` queries of the form: when you begin on planet `x` and travel through `k` teleporters, which planet will you reach?

### Input

The first input line has two integers `n` and `q`: the number of planets and queries. The planets are numbered `1, 2, ..., n`.

The second line has `n` integers `t1, t2, ..., tn`: for each planet, the destination of the teleporter. It is possible that `ti = i`.

Finally, there are `q` lines describing the queries. Each line has two integers `x` and `k`: you start on planet `x` and travel through `k` teleporters.

### Output

Print the answer to each query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= ti <= n`
- `1 <= x <= n`
- `0 <= k <= 1e9`

### Examples

**Input**
```
4 3
2 1 1 4
1 2
3 4
4 1
```

**Output**
```
1
2
4
```

---

## What this problem really is

Functional graph: each node out-degree `1`. Answer “after `k` steps from `x`” with `k` up to `1e9` and many queries.

## What to use

| Tool | Why |
|------|-----|
| Binary lifting `up[j][i]` | `up[j][i]` = where you are from `i` after `2^j` teleports |
| Bit decomposition of `k` | Jump by powers of two |
| `LOG ≈ 30` | `2^30 > 1e9` |

## Approach

**Root idea:** precompute doubling jumps. Any `k` is a sum of distinct powers of two; apply those jumps in order.

**Mental picture:** from each planet, mark where you land after 1, 2, 4, 8, … hops. To travel `k` hops, use the binary digits of `k` like binary lifting on trees (here the “parent” is the unique teleporter).

**Data structures**

- `up[0][i] = t[i]` (or 0-index carefully).
- `up[j][i] = up[j-1][ up[j-1][i] ]` for `j = 1 .. LOG-1`.

**Query `(x, k)`**

```
cur = x
for j = 0 .. LOG-1:
  if (k >> j) & 1:
    cur = up[j][cur]
answer = cur
```

(`k = 0` → stay at `x`.)

**Steps**

1. Read `t[1..n]`, build `up`.
2. Answer each query with the bit loop.
3. Print one planet per query.

## Walkthrough (on a sample)

`t = [2,1,1,4]` (1-based).

- Start `1`, `k=2`: `1→2→1` → **`1`**
- Start `3`, `k=4`: `3→1→2→1→2` → **`2`**
- Start `4`, `k=1`: `4→4` → **`4`**

## Pitfalls

- Simulating `k` steps one-by-one — TLE for `1e9`.
- Off-by-one in 0/1-based planet indexing.
- `k = 0` must return `x`.

## Complexity

- Time: `O((n + q) log K)` with `K = 1e9`  
- Space: `O(n log K)`

## Key takeaway

**Outdegree-1 graph + huge steps → binary lifting.** Precompute `2^j` jumps; apply bits of `k`.
