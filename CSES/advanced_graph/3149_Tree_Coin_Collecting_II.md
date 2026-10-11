# [3149 — Tree Coin Collecting II](https://cses.fi/problemset/task/3149)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** tree, Steiner tree, LCA  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a tree with `n` nodes. Some nodes contain a coin.

Your task is to answer `q` queries of the form: what is the shortest length of a path from node `a` to node `b` that visits all nodes with coins?

### Input

The first line contains two integers `n` and `q`: the number of nodes and queries. The nodes are numbered `1, 2, ..., n`.

The second line contains `n` integers `c1, c2, ..., cn`. If `ci = 1`, node `i` has a coin. If `ci = 0`, node `i` doesn't have a coin. You can assume at least one node has a coin.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

Finally, there are `q` lines describing the queries. Each line contains two integers `a` and `b`: the start and end nodes.

### Output

Print `q` integers: the answers to the queries.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 4
1 0 0 1 0
2 4
2 3
1 3
3 5
1 5
3 2
4 4
5 5
```

**Output**
```
6
5
6
8
```

---

## What this problem really is

For each `(a,b)`, length of the shortest walk from `a` to `b` that visits every coin — the Chinese-Postman tour of the Steiner tree of `{a,b} ∪ coins`, ending at `b`.

## What to use

| Tool | Why |
|------|-----|
| Coin Steiner tree `T` | Minimal subtree covering all coins (fixed) |
| Distances into `T` | Adding `a` and `b` only attaches their paths into `T` |
| Tree walk formula | Cover every Steiner edge twice, except path `a-b` once: `2*S - dist(a,b)` |

## Approach

**Root idea:** any optimal walk must traverse every edge of the Steiner tree of the terminals `{a,b} ∪ coins`. On a tree, the cheapest `a`-start `b`-end tour that covers those edges has length `2 * (Steiner weight) - dist(a,b)`.

**Mental picture:** paint the unique subtree that touches all coins and both endpoints. You must walk every painted edge twice (out and back), but along the direct road from `a` to `b` you can leave those edges traversed only once.

**How that becomes the method**

1. Mark coin nodes. Build the Steiner tree `T` of all coins:
   - root the tree; an edge to a child is in `T` iff the child subtree contains ≥1 coin and the outside also contains ≥1 coin.
   - `|T|` = number of such edges (unweighted tree).
2. For every node `v`, compute `distT[v]` = distance to the nearest node of `T`, and `entry[v]` = that projection (multi-source BFS from all nodes already on `T`, or DP).
3. Nodes on `T` have `distT = 0`, `entry = self` (or any representative on `T`).
4. For query `(a,b)` with LCA tooling for `dist(a,b)`:
   - Let `S_extra` = weight of `path(a,entry[a]) ∪ path(b,entry[b])`:
     - if `entry[a] != entry[b]`: `distT[a] + distT[b]`;
     - if `entry[a] == entry[b] == e`: `distT[a] + distT[b] - 2 * dist(lca(a,b), e)` (shared prefix toward the same entry).
   - `S = |T| + S_extra`.
   - `answer = 2 * S - dist(a,b)`.

**Data structures:** adj; subtree coin counts; BFS for `distT`/`entry`; LCA tables.

## Walkthrough (on a sample)

Coins at `1,4` → `T` is path `1-3-2-4`, `|T|=3`. Node `5` has `distT=1`.

| query | `S` | `dist` | `2S - dist` |
|-------|-----|--------|-------------|
| `1,5` | `3+1=4` | `2` | `6` |
| `3,2` | `3` | `1` | `5` |
| `4,4` | `3` | `0` | `6` |
| `5,5` | `3+1=4` | `0` | `8` |

## Pitfalls

- Using Coin Collecting I’s “one coin” formula — here every coin is mandatory.
- Double-counting when `a` and `b` hang off the same side of `T` (must subtract the shared prefix).
- Weighted vs unweighted: this CSES tree is unweighted (each edge length `1`).

## Complexity

- Time: `O((n + q) log n)` with LCA
- Space: `O(n log n)`

## Key takeaway

Visit-all-coins `a→b` walk = `2 * (Steiner weight of coins∪{a,b}) - dist(a,b)` on a tree.
