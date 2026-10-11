# [1703 — Critical Cities](https://cses.fi/problemset/task/1703)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** dominator tree, directed graphs  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `m` flight connections between them. A city is called a critical city if it appears on every route from a city to another city.

Your task is to find all critical cities from Syrjälä to Lehmälä.

### Input

The first input line has two integers `n` and `m`: the number of cities and flights. The cities are numbered `1,2,...,n`. City `1` is Syrjälä, and city `n` is Lehmälä.

Then, there are `m` lines describing the connections. Each line has two integers `a` and `b`: there is a flight from city `a` to city `b`. All flights are one-way.

You may assume that there is a route from Syrjälä to Lehmälä.

### Output

First print an integer `k`: the number of critical cities. After this, print `k` integers: the critical cities in increasing order.

### Constraints

- `2 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a,b <= n`

### Examples

**Input**
```
5 5
1 2
2 3
2 4
3 5
4 5
```

**Output**
```
3
1 2 5
```

---

## What this problem really is

List all vertices that lie on **every** path from `1` to `n` in a directed graph (dominators of `n` with source `1`), including `1` and `n`.

## What to use

| Tool | Why |
|------|-----|
| Dominator tree | Classic structure: `idom[v]` = unique immediate dominator of `v` from source `1` |
| Lengauer-Tarjan / simplified LT | Builds dominators in nearly linear time for `n,m ~ 1e5` |
| Climb from `n` to `1` | Critical cities = ancestors of `n` in the dominator tree (plus `n`) |

## Approach

**Root idea:** `v` is critical for `1 → n` iff every path from `1` to `n` goes through `v`, i.e. `v` dominates `n`. Dominators form a tree rooted at `1`; walk from `n` up via immediate dominators.

**Mental picture:** the dominator tree is a “must-pass hierarchy.” Anything on the chain from the sink up to the source is unavoidable.

**How that becomes the method**

1. Build the directed graph with edges `a → b`.
2. Compute the dominator tree rooted at source `1` (Lengauer-Tarjan or a verified library implementation): for every node reachable from `1`, get `idom[v]` (parent in the dominator tree).
3. Start at `n`; while `n != 0`, push the node into a list and set `n = idom[n]` (until you reach `1`, whose idom is none).
4. Sort the list and print size + nodes.

Always include `1` and `n`. In the sample, `3` and `4` are bypassable via the other branch through `2`.

**Data structures:** adj + reverse adj as required by LT; arrays `sdom`, `idom`, DFS numbering; answer vector.

## Walkthrough (on a sample)

Paths `1→2→3→5` and `1→2→4→5`. Intersection of all paths: `{1,2,5}`. Node `2` dominates `5`; `3`/`4` do not.

## Pitfalls

- Using undirected bridges / articulation points — the graph is directed.
- Only checking nodes on one shortest path.
- Dominator algorithms are easy to get wrong — test on small DAGs and diamonds.

## Complexity

- Time: `O((n + m) log n)` typical LT implementations (or near-linear variants)
- Space: `O(n + m)`

## Key takeaway

Critical cities on all `1↝n` paths = dominators of `n` from source `1` = the dominator-tree chain from `n` up to `1`.
