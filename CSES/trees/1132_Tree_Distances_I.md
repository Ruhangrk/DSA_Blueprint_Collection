# [1132 — Tree Distances I](https://cses.fi/problemset/task/1132)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** trees, rerooting, diameter endpoints  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a tree consisting of `n` nodes.

Your task is to determine for each node the maximum distance to another node.

### Input

The first input line contains an integer `n`: the number of nodes. The nodes are numbered `1, 2, ..., n`.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

### Output

Print `n` integers: for each node `1, 2, ..., n`, the maximum distance to another node.

### Constraints

- `1 <= n <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5
1 2
1 3
3 4
3 5
```

**Output**
```
2 3 2 3 3
```

---

## What this problem really is

For every node `u`, compute eccentricity: `max_v dist(u,v)`. Doing a BFS from each `u` is `O(n^2)` — too slow.

## What to use

| Tool | Why |
|------|-----|
| Diameter endpoints `A`, `B` | Eccentricity of `u` is `max(dist(u,A), dist(u,B))` |
| Two BFS distance arrays | After finding `A,B`, fill `distA[]`, `distB[]` |
| Rerooting DP (alt.) | Also `O(n)`: down-max and up-max heights |

## Approach

**Root idea:** in a tree, the farthest node from any `u` is always one of the two diameter endpoints. So find diameter ends `A,B`, then for each `u` answer `max(dist(u,A), dist(u,B))`.

**Mental picture:** the tree’s “poles” are `A` and `B`; from anywhere, the furthest point is whichever pole is farther.

**Steps**

1. Two-pass diameter → endpoints `A`, `B`.
2. BFS from `A` → `distA[1..n]`; BFS from `B` → `distB[1..n]`.
3. For each `u`, print `max(distA[u], distB[u])`.

**Reroot alternative:** DFS for best downward distance; second DFS pushes best distance from outside the subtree.

## Walkthrough (on a sample)

Diameter ends e.g. `2` and `4` (or `5`). Node `1`: max dist to an end = **2**. Node `2`: **3**. Matches `2 3 2 3 3`.

## Pitfalls

- Taking only `dist(u,A)` — need the max of both ends.
- Assuming the diameter is unique — any diameter’s endpoints work.
- `n=1` → answer `0`.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Max distance from every node = `max(dist to diameter end A, dist to end B)`** after one diameter computation.
