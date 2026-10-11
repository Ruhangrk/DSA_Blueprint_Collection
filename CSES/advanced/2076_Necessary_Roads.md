# [2076 — Necessary Roads](https://cses.fi/problemset/task/2076)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** bridges, DFS tree, undirected graph  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `m` roads between them. There is a route between any two cities.

A road is called necessary if there is no route between some two cities after removing that road. Your task is to find all necessary roads.

### Input

The first input line has two integers `n` and `m`: the number of cities and roads. The cities are numbered `1,2,...,n`.

After this, there are `m` lines that describe the roads. Each line has two integers `a` and `b`: there is a road between cities `a` and `b`. There is at most one road between two cities, and every road connects two distinct cities.

### Output

First print an integer `k`: the number of necessary roads. After that, print `k` lines that describe the roads. You may print the roads in any order.

### Constraints

- `2 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 5
1 2
1 4
2 4
3 5
4 5
```

**Output**
```
2
3 5
4 5
```

---

## What this problem really is

List all **bridges** of a connected undirected graph (edges whose removal disconnects the graph).

## What to use

| Tool | Why |
|------|-----|
| DFS tree + `tin` / `low` | Classic bridge-finding |
| Ignore parent edge | Do not treat the tree edge back to parent as a back-edge |
| Edge list output | Collect bridges as you find them |

## Approach

**Root idea:** an edge `u-v` (tree edge, `u` parent of `v`) is a bridge iff no back-edge from `v`’s subtree reaches `u` or above — i.e. `low[v] > tin[u]`.

**Mental picture:** DFS builds a spanning tree. Extra edges are “safety ropes.” A tree edge is necessary exactly when its subtree has no safety rope climbing over that edge.

**How that becomes the method**

1. Run DFS from any start (graph is connected).
2. `tin[u]` = discovery time; `low[u] = min(tin[u], tin of back-neighbors, low of children)`.
3. For child `v` of `u`, if `low[v] > tin[u]`, edge `u-v` is a bridge.
4. Print count and the bridge endpoints (either order).

**Data structures:** adj list; arrays `tin`, `low`, `timer`; vector of bridge pairs.

## Walkthrough (on a sample)

Triangle `1-2-4` has no bridge inside. Path `4-5-3` makes `4-5` and `3-5` bridges — removing either splits off part of the chain.

## Pitfalls

- Using `low[v] >= tin[u]` (that is for articulation points, not bridges).  
- Treating the parent link as a back-edge.  
- Multiedges (none here) would need edge ids.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Necessary roads = bridges** — DFS `tin`/`low` with `low[child] > tin[u]`.
