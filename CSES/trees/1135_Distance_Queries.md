# [1135 — Distance Queries](https://cses.fi/problemset/task/1135)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** LCA, tree distances  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a tree consisting of `n` nodes.

Your task is to process `q` queries of the form: what is the distance between nodes `a` and `b`?

### Input

The first input line contains two integers `n` and `q`: the number of nodes and queries. The nodes are numbered `1, 2, ..., n`.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

Finally, there are `q` lines describing the queries. Each line contains two integers `a` and `b`: what is the distance between nodes `a` and `b`?

### Output

Print `q` integers: the answer to each query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 3
1 2
1 3
3 4
3 5
1 3
2 5
1 4
```

**Output**
```
1
3
2
```

---

## What this problem really is

Unweighted tree, many pairwise distance queries. Use the identity with LCA depths.

## What to use

| Tool | Why |
|------|-----|
| LCA (binary lifting) | Meet point of paths `a` and `b` |
| Depth array | Distances from an arbitrary root |
| Formula | `dist(a,b) = depth[a] + depth[b] - 2*depth[lca(a,b)]` |

## Approach

**Root idea:** path `a → b` goes up to the LCA then down. Edge count = up-steps + down-steps = `(depth[a]-depth[lca]) + (depth[b]-depth[lca])`.

**Mental picture:** two hikers climb to their shared fork, then one descends the other branch — total steps = both climbs from the fork.

**Steps**

1. Root the tree at `1` (any root). Build LCA structure + `depth[]`.
2. For each query `(a,b)`: print `depth[a] + depth[b] - 2*depth[lca(a,b)]`.

## Walkthrough (on a sample)

`dist(1,3)=1`. `dist(2,5)`: LCA `1`, depths `1+2-0=3`. `dist(1,4)=2`.

## Pitfalls

- Forgetting the factor `2` on `depth[lca]`.
- Weighted edges would need dist-from-root sums — here edges weight `1`, so depth suffices.
- `a == b` → distance `0`.

## Complexity

- Time: `O((n+q) log n)`  
- Space: `O(n log n)`

## Key takeaway

**Tree distance queries = `depth[a] + depth[b] - 2*depth[LCA(a,b)]`.**
