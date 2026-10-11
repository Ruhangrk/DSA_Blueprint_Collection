# [2077 — Necessary Cities](https://cses.fi/problemset/task/2077)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** articulation points, DFS tree, tin/low  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `m` roads between them. There is a route between any two cities.

A city is called necessary if there is no route between some other two cities after removing that city (and adjacent roads). Your task is to find all necessary cities.

### Input

The first input line has two integers `n` and `m`: the number of cities and roads. The cities are numbered `1,2,...,n`.

After this, there are `m` lines that describe the roads. Each line has two integers `a` and `b`: there is a road between cities `a` and `b`. There is at most one road between two cities, and every road connects two distinct cities.

### Output

First print an integer `k`: the number of necessary cities. After that, print a list of `k` cities. You may print the cities in any order.

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
4 5
```

---

## What this problem really is

List all **articulation points** (cut vertices) of a connected undirected graph.

## What to use

| Tool | Why |
|------|-----|
| DFS tree + `tin` / `low` | Standard cut-vertex criteria |
| Root special case | Root is cut iff it has ≥2 DFS children |
| Non-root rule | `u` is cut if some child `v` has `low[v] >= tin[u]` |

## Approach

**Root idea:** removing `u` disconnects the graph iff some subtree hanging off `u` in the DFS tree has no back-edge that climbs strictly above `u`.

**Mental picture:** DFS builds a spanning tree. Back-edges are bypass ropes. A city is necessary when some neighborhood relies on it as the only gateway upward.

**How that becomes the method**

1. DFS from any node; compute discovery `tin[u]` and `low[u]`.
2. Root: count children in the DFS tree; if `>= 2`, mark as articulation.
3. Non-root `u`: if any child `v` satisfies `low[v] >= tin[u]`, mark `u`.
4. Print distinct marked cities.

Contrast with bridges: bridges use strict `low[v] > tin[u]` on edges; cut vertices use `>=` on vertices (plus root rule).

**Data structures:** adj list; `tin`, `low`, `timer`; bool `is_cut[n+1]`.

## Walkthrough (on a sample)

Removing `4` or `5` splits the triangle side from `{3}` / the rest. `1,2,3` are not cut vertices.

## Pitfalls

- Applying the non-root rule to the root.  
- Using `>` instead of `>=` for vertices.  
- Marking the same vertex many times without deduping in output count.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Necessary cities = articulation points** — DFS `tin`/`low`, with a separate root-children rule.
