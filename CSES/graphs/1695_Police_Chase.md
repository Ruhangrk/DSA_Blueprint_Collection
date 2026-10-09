# [1695 — Police Chase](https://cses.fi/problemset/task/1695)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, min cut, max flow  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Kaaleppi has just robbed a bank and is now heading to the harbor. However, the police wants to stop him by closing some streets of the city.

What is the minimum number of streets that should be closed so that there is no route between the bank and the harbor?

### Input

The first input line has two integers `n` and `m`: the number of crossings and streets. The crossings are numbered `1, 2, ..., n`. The bank is located at crossing `1`, and the harbor is located at crossing `n`.

After this, there are `m` lines that describing the streets. Each line has two integers `a` and `b`: there is a street between crossings `a` and `b`. All streets are two-way streets, and there is at most one street between two crossings.

### Output

First print an integer `k`: the minimum number of streets that should be closed. After this, print `k` lines describing the streets. You can print any valid solution.

### Constraints

- `2 <= n <= 500`
- `1 <= m <= 1000`
- `1 <= a, b <= n`

### Examples

**Input**
```
4 5
1 2
1 3
2 3
3 4
1 4
```

**Output**
```
2
3 4
1 4
```

---

## What this problem really is

Undirected graph: **minimum edge cut** between `1` and `n` (fewest edges to delete to disconnect `1` from `n`). Also output one such set of edges.

## What to use

| Tool | Why |
|------|-----|
| Max-flow / min-cut | With unit capacities, min cut size = max flow |
| Capacity `1` per undirected edge | Each street closed once |
| Residual BFS after max flow | S-side of the cut = reachable from `1` in residual; cut edges go S→T |

## Approach

**Root idea:** min number of streets to separate bank from harbor = min edge cut = max flow if every undirected street has capacity `1`.

**Mental picture:** each street is a unit-capacity pipe both ways (model carefully); saturate the network; the bottleneck cut is the cheapest set of streets to close.

**Modeling undirected edges:** for street `{a,b}`, add residual capacities `1` in both directions (standard undirected unit network), or one undirected edge object with capacity 1 used either way — consistent Dinic setup.

**Steps**

1. Build flow network, capacities `1` on streets, source `1`, sink `n`.
2. Compute max flow → `k`.
3. BFS/DFS from `1` in the **residual** graph; mark reachable set `S`.
4. Every original street with one end in `S` and the other not in `S` is a cut edge — print those (should be exactly `k` of them).

## Walkthrough (on a sample)

Min cut size **`2`**; one certificate closes `3-4` and `1-4`, disconnecting `1` from `4`.

## Pitfalls

- Vertex cut vs edge cut — this problem is **edges**.
- After flow, listing all saturated edges blindly may list more than a min cut; use residual reachability.
- Undirected modeling mistakes (double-counting capacity).

## Complexity

- Time: max-flow on `n<=500`, `m<=1000` (Dinic fine)  
- Space: `O(n + m)`

## Key takeaway

**Min streets to disconnect `s`–`t` = min edge cut = unit-capacity max flow.** Recover edges via residual source-side vs sink-side.
