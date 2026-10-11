# [2078 — Eulerian Subgraphs](https://cses.fi/problemset/task/2078)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** cycle space, GF(2), connected components  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an undirected graph that has `n` nodes and `m` edges.

We consider subgraphs that have all nodes of the original graph and some of its edges. A subgraph is called Eulerian if each node has even degree.

Your task is to count the number of Eulerian subgraphs modulo `1e9+7`.

### Input

The first input line has two integers `n` and `m`: the number of nodes and edges. The nodes are numbered `1,2,...,n`.

After this, there are `m` lines that describe the edges. Each line has two integers `a` and `b`: there is an edge between nodes `a` and `b`. There is at most one edge between two nodes, and each edge connects two distinct nodes.

### Output

Print the number of Eulerian subgraphs modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e5`
- `0 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
4 3
1 2
1 3
2 3
```

**Output**
```
2
```

Explanation: You can either keep or remove all edges, so there are two possible Eulerian subgraphs.

---

## What this problem really is

Count edge-subsets where every degree is even (over GF(2): vectors in the **cycle space**), including the empty subset. Mod `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| Cycle-space dimension | `#Eulerian = 2^(dim)` |
| Formula `m - n + c` | Dim of cycle space for an undirected graph with `c` components |
| Modular pow | `pow(2, m-n+c, MOD)` |

## Approach

**Root idea:** choosing edges so all degrees are even is exactly the binary cycle space. Its dimension is `m - n + c` (one independent “circuit choice” per extra edge beyond a forest).

**Mental picture:** a spanning forest uses `n - c` edges and has only the empty even subgraph. Each extra edge beyond the forest doubles the number of even-degree subsets (you may xor that edge’s fundamental cycle in or out).

**How that becomes the method**

1. Build the undirected graph; count connected components `c` (DFS/Union-Find on `n` nodes — isolated vertices count).
2. Answer `2^(m - n + c) mod (1e9+7)`.

Empty graph (`m=0`): `c=n`, exponent `0`, answer `1` (only the empty edge set).

**Data structures:** adj or DSU; 64-bit modpow.

## Walkthrough (on a sample)

Triangle + isolated node: `n=4`, `m=3`, `c=2` → exponent `3-4+2=1` → `2`. Matches “all edges or none” on the triangle.

## Pitfalls

- Using `m-n+1` (single component only).  
- Forgetting isolated vertices in `c`.  
- Thinking Eulerian requires the subgraph to be connected — here only even degrees matter.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Eulerian spanning subgraphs = cycle space** — count is `2^(m - n + c)` mod `1e9+7`.
