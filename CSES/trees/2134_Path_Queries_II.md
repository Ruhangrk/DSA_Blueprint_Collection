# [2134 — Path Queries II](https://cses.fi/problemset/task/2134)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** HLD, segment tree, path maximum  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a tree consisting of `n` nodes. The nodes are numbered `1, 2, ..., n`. Each node has a value.

Your task is to process following types of queries:

1. change the value of node `s` to `x`
2. find the maximum value on the path between nodes `a` and `b`

### Input

The first input line contains two integers `n` and `q`: the number of nodes and queries. The nodes are numbered `1, 2, ..., n`.

The next line has `n` integers `v1, v2, ..., vn`: the value of each node.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

Finally, there are `q` lines describing the queries. Each query is either of the form `"1 s x"` or `"2 a b"`.

### Output

Print the answer to each query of type `2`.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= a, b, s <= n`
- `1 <= vi, x <= 1e9`

### Examples

**Input**
```
5 3
2 4 1 3 3
1 2
1 3
2 4
2 5
2 3 5
1 2 2
2 3 5
```

**Output**
```
4
3
```

---

## What this problem really is

Point update of node values + **path maximum** queries on a tree. Needs heavy-light decomposition (or Euler + more structure); plain subtree Fenwick is not enough.

## What to use

| Tool | Why |
|------|-----|
| Heavy-Light Decomposition | Path splits into `O(log n)` contiguous chains |
| Segment tree (max) | Point update + range max on chains |
| LCA | Know where the path bends |

## Approach

**Root idea:** decompose the tree into heavy paths. Map each node to a position in a segtree line. A path `a–b` becomes a few intervals; take the max across them (and include both endpoints’ values).

**Mental picture:** highways (heavy paths) plus side roads; travel `a` to LCA and `b` to LCA as a handful of highway segments, reading the max sign on each.

**Data structures**

- HLD arrays: `parent`, `depth`, `heavy`, `head`, `pos`.
- Segtree over `pos` order storing node values.

**Steps**

1. DFS sizes; mark heavy child; decompose; assign `pos`.
2. Build segtree from values at `pos[u]`.
3. Type `1`: point-set `pos[s]` to `x`.
4. Type `2`: while `head[a] != head[b]`, lift the deeper head, query max on that chain segment; finally query the remaining chain between `a` and `b`.

## Walkthrough (on a sample)

Path `3–5`: nodes `3,1,2,5`? Tree edges `1-2,1-3,2-4,2-5` — path `3-1-2-5`, values `1,2,4,3` → max **4**. After node `2` becomes `2`, max on same path is **3**.

## Pitfalls

- Forgetting to include LCA’s value exactly once.
- Mixing edge-valued HLD with node-valued queries.
- Not swapping so the deeper chain is lifted first.

## Complexity

- Time: `O((n+q) log^2 n)` typical HLD  
- Space: `O(n)`

## Key takeaway

**Path max with updates = HLD + segment tree** — path → `O(log n)` ranges, take max.
