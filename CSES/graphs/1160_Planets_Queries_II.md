# [1160 — Planets Queries II](https://cses.fi/problemset/task/1160)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, functional graph, binary lifting, cycles  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are playing a game consisting of `n` planets. Each planet has a teleporter to another planet (or the planet itself).

You have to process `q` queries of the form: You are now on planet `a` and want to reach planet `b`. What is the minimum number of teleportations?

### Input

The first input line contains two integers `n` and `q`: the number of planets and queries. The planets are numbered `1, 2, ..., n`.

The second line contains `n` integers `t1, t2, ..., tn`: for each planet, the destination of the teleporter.

Finally, there are `q` lines describing the queries. Each line has two integers `a` and `b`: you are now on planet `a` and want to reach planet `b`.

### Output

For each query, print the minimum number of teleportations. If it is not possible to reach the destination, print `-1`.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 3
2 3 2 3 2
1 2
1 3
1 4
```

**Output**
```
1
2
-1
```

---

## What this problem really is

Functional graph (outdegree 1). Min steps along unique teleports from `a` to `b`, or `-1`. Many queries, large `n`.

## What to use

| Tool | Why |
|------|-----|
| Component structure | Each component = trees feeding into one cycle |
| Binary lifting / cycle IDs | Jump and compare positions on cycle/tails |
| Precompute `depth`, `cyc_id`, `cyc_pos`, `cyc_len` | Answer queries in `O(log n)` |

## Approach

**Root idea:** from any start you eventually enter exactly one cycle and then loop. You can reach `b` from `a` only if they share that future, and `b` lies on the unique path of teleports starting at `a` (on the tail and/or the cycle).

**Mental picture:** arrows always forward. Tails flow into a loop. From `a`, the reachable set is a ray that hits the cycle and then circles forever.

**Data structures / preprocessing**

1. Find for each node: whether on a cycle; cycle index; position on cycle; cycle length; distance to cycle (`tail` depth).
2. Binary lifting `up[j][i]` for `2^j` jumps (same as Planets Queries I).
3. Helper: after `k` jumps from `x`, which node (lifting).

**Query `a → b` (high level)**

- If `a == b` → `0`.
- If `b` is not on `a`’s eventual cycle and not on `a`’s path into that cycle → `-1`.
- If both on the same cycle: distance = clockwise steps from `a` to `b` on the cycle (if `a` already on cycle), or `dist_to_cycle(a) +` clockwise from cycle entry to `b`.
- If `b` is on the tail above the cycle: `b` must be an ancestor along the unique path into the cycle — use lifting/depth so that jumping `depth[a]-depth[b]` from `a` lands on `b`.

(Implementations vary: often reverse edges on tails as a rooted tree into the cycle, then LCA-style checks.)

**Steps**

1. Build `t[i]`, preprocess cycles + lifting.
2. Answer each query with the case analysis above.
3. Print distances or `-1`.

## Walkthrough (on a sample)

`t = [2,3,2,3,2]`. Cycle involves `2 ↔ 3`. From `1→2` in **1** step; `1→2→3` in **2**; `4` is on another branch into `3` but not reachable from `1` → **`-1`**.

## Pitfalls

- Assuming undirected reachability — only follow teleporters forward.
- Wrong cycle distance direction — only one way around.
- `a == b` edge case.
- `O(n)` per query — must preprocess.

## Complexity

- Time: `O((n + q) log n)` after linear cycle finding  
- Space: `O(n log n)`

## Key takeaway

**Functional-graph distance queries:** decompose into tails + cycles, then jump with binary lifting and measure on-cycle offsets.
