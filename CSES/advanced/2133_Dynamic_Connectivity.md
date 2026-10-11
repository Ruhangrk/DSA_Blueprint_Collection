# [2133 — Dynamic Connectivity](https://cses.fi/problemset/task/2133)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** DSU rollback, segment tree offline, dynamic connectivity  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider an undirected graph that consists of `n` nodes and `m` edges. There are two types of events that can happen:

1. A new edge is created between nodes `a` and `b`.
2. An existing edge between nodes `a` and `b` is removed.

Your task is to report the number of components after every event.

### Input

The first input line has three integers `n`, `m` and `k`: the number of nodes, edges and events.

After this there are `m` lines describing the edges. Each line has two integers `a` and `b`: there is an edge between nodes `a` and `b`. There is at most one edge between any pair of nodes.

Then there are `k` lines describing the events. Each line has the form `t a b` where `t` is `1` (create a new edge) or `2` (remove an edge). A new edge is always created between two nodes that do not already have an edge between them, and only existing edges can get removed.

### Output

Print `k+1` integers: first the number of components before the first event, and after this the new number of components after each event.

### Constraints

- `2 <= n <= 1e5`
- `1 <= m, k <= 1e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 3 3
1 4
2 3
3 5
1 2 5
2 3 5
1 1 2
```

**Output**
```
2 2 2 1
```

---

## What this problem really is

Fully dynamic undirected connectivity with edge insertions/deletions; report component count after each update (and initially).

## What to use

| Tool | Why |
|------|-----|
| Offline segment tree over time | Each edge lives on a time interval `[add, rem)` |
| DSU with rollback | Add edges down the segment tree; undo on the way up |
| Component counter | Start at `n`; decrement on successful unions |

## Approach

**Root idea:** deletions make online union-find hard, but offline you know each edge’s lifetime. Put “edge present” as a segment on a time line; DFS the segment tree, unioning edges at nodes, recording answers at leaves, then rolling back.

**Mental picture:** time is an axis. Each edge is a horizontal bar while it exists. A segment tree chops the axis; at each piece you temporarily glue the cities that have a bar covering that piece, read the component count at each event moment, then unglue.

**How that becomes the method**

1. Time `0` = initial graph; times `1..k` = after each event. Track for every undirected edge the interval it exists (map of pairs → last add time).
2. When an edge is removed at time `t`, push interval `[last_add, t)` onto the segment tree; on insert, set `last_add = t`.
3. After all events, close open intervals to `[last_add, k+1)`.
4. DSU rollback DFS on the segment tree: at node, union all edges stored there (save undo stack); if leaf corresponding to time `t`, record `components`; else recurse children; pop undos.
5. Print answers for times `0..k`.

**Data structures:** segment tree of edge lists; DSU with parent/size history stack; `map`/`hash` of edge → add time.

## Walkthrough (on a sample)

Initial edges connect into `2` components. Events add/remove without changing count until the last add merges to `1`.

## Pitfalls

- Not normalizing edge endpoints `(min,max)`.  
- Permanent DSU without rollback.  
- Off-by-one on half-open time intervals.  
- Multiedges (none here).

## Complexity

- Time: `O((m+k) log (m+k) * α(n))`  
- Space: `O((m+k) log (m+k))`

## Key takeaway

**Offline dynamic connectivity = segment tree over time + DSU rollback**, report components at each time leaf.
