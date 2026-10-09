# [1130 — Tree Matching](https://cses.fi/problemset/task/1130)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** trees, DP, matching, greedy  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a tree consisting of `n` nodes.

A matching is a set of edges where each node is an endpoint of at most one edge. What is the maximum number of edges in a matching?

### Input

The first input line contains an integer `n`: the number of nodes. The nodes are numbered `1, 2, ..., n`.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

### Output

Print one integer: the maximum number of pairs.

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
2
```

Explanation: One possible matching is `(1,2)` and `(3,4)`.

---

## What this problem really is

Maximum matching in a **tree** (not a general graph) — size = max number of disjoint edges.

## What to use

| Tool | Why |
|------|-----|
| Tree DP | `dp[u][0/1]` = best matching in subtree with `u` free / matched to a child |
| Or greedy | DFS: match a leaf to its parent when possible |
| Root arbitrarily | Tree is undirected |

## Approach

**Root idea (DP):** at node `u`, either leave `u` unmatched and take the sum of best values from children, or match `u` to exactly one child `v` (adding `1 + dp[v][0]` style) plus bests from other children.

More cleanly:

- `dp[u][0]` = max matching in `u`’s subtree when `u` is **not** matched downward.
- `dp[u][1]` = max when `u` **is** matched to some child.

```
dp[u][0] = sum over children v of max(dp[v][0], dp[v][1])
dp[u][1] = max over children v of
  ( dp[u][0] - max(dp[v][0], dp[v][1]) + dp[v][0] + 1 )
```

Answer = `max(dp[root][0], dp[root][1])`.

**Greedy picture:** walk from leaves upward; whenever a node still free has a free parent edge, take it — also optimal on trees.

**Mental picture:** pair as many parent–child links as you can without sharing vertices — DP tries “use this edge or not” at each node.

**Steps**

1. Build undirected adjacency.
2. DFS from node `1`, compute the two DP values.
3. Print the max at the root.

## Walkthrough (on a sample)

Star-like: matching `(1,2)` and `(3,4)` (or `(3,5)`) → size **2**. Cannot take three edges on 5 nodes.

## Pitfalls

- Using general blossom matching — overkill and harder at `2e5`.
- Double-counting an edge from both ends.
- Forgetting a node can match to at most one child.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Max matching on a tree = tree DP (matched / unmatched at root of subtree)** or leaf-toward-root greedy pairing.
