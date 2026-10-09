# [1137 — Subtree Queries](https://cses.fi/problemset/task/1137)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** Euler tour, Fenwick / segment tree, subtree sum  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a rooted tree consisting of `n` nodes. The nodes are numbered `1, 2, ..., n`, and node `1` is the root. Each node has a value.

Your task is to process following types of queries:

1. change the value of node `s` to `x`
2. calculate the sum of values in the subtree of node `s`

### Input

The first input line contains two integers `n` and `q`: the number of nodes and queries. The nodes are numbered `1, 2, ..., n`.

The next line has `n` integers `v1, v2, ..., vn`: the value of each node.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

Finally, there are `q` lines describing the queries. Each query is either of the form `"1 s x"` or `"2 s"`.

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
4 2 5 2 1
1 2
1 3
3 4
3 5
2 3
1 5 3
2 3
```

**Output**
```
8
10
```

---

## What this problem really is

Point update on a node value + subtree sum queries on a static rooted tree.

## What to use

| Tool | Why |
|------|-----|
| Euler tour / DFS order | Subtree of `s` = contiguous segment `[tin[s], tout[s]]` |
| Fenwick or segtree | Range sum on that segment; point update at `tin[s]` |
| `long long` | Large sums |

## Approach

**Root idea:** flatten the tree by DFS entry times. Every subtree becomes an array interval. Then the problem is ordinary range-sum + point-update.

**Mental picture:** write nodes in the order you first visit them; a whole department sits in one consecutive block on that list.

**Data structures**

- `tin[u]`, `tout[u]` (or `tin[u]` and `sz[u]` with interval `[tin, tin+sz-1]`).
- Fenwick on positions `1..n` storing node values at `tin[u]`.

**Steps**

1. Root at `1`; DFS assign `tin` / subtree size.
2. Build BIT with `add(tin[u], v[u])` for all `u`.
3. Type `1 s x`: `add(tin[s], x - v[s])`; `v[s]=x`.
4. Type `2 s`: print `sum(tin[s], tin[s]+sz[s]-1)`.

## Walkthrough (on a sample)

Subtree of `3` is `{3,4,5}` values `5+2+1=8`. After setting node `5` to `3`, sum `5+2+3=10`.

## Pitfalls

- Updating the wrong index (node id instead of `tin`).
- Inclusive/exclusive `tout` confusion.
- Not rerooting / assuming input edges are parent→child already.

## Complexity

- Time: `O((n+q) log n)`  
- Space: `O(n)`

## Key takeaway

**Subtree sum with updates = Euler tour + Fenwick/segtree** on the DFS interval of the subtree.
