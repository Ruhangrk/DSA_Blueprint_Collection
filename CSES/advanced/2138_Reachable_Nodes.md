# [2138 — Reachable Nodes](https://cses.fi/problemset/task/2138)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** DAG, bitset, DP on DAG  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A directed acyclic graph consists of `n` nodes and `m` edges. The nodes are numbered `1,2,...,n`.

Calculate for each node the number of nodes you can reach from that node (including the node itself).

### Input

The first input line has two integers `n` and `m`: the number of nodes and edges.

Then there are `m` lines describing the edges. Each line has two distinct integers `a` and `b`: there is an edge from node `a` to node `b`.

### Output

Print `n` integers: for each node the number of reachable nodes.

### Constraints

- `1 <= n <= 5e4`
- `1 <= m <= 1e5`

### Examples

**Input**
```
5 6
1 2
1 3
1 4
2 3
3 5
4 5
```

**Output**
```
5 3 2 2 1
```

---

## What this problem really is

On a DAG, for every vertex `v`, output `|reachable(v)|` including `v`. Need something faster than `n` separate DFS scans in the worst case, but `n=5e4` allows heavy bitset DP.

## What to use

| Tool | Why |
|------|-----|
| `bitset` per node | Reachability set; OR along edges |
| Memoized DFS / topo DP | DAG → process children before parent merge |
| `.count()` | Size of the reachability set |

## Approach

**Root idea:** if `reach[v]` is the bitset of nodes reachable from `v`, then `reach[v] = {v} ∪ (OR of reach[u] over edges v→u)`. Acyclicity makes this a well-founded DP.

**Mental picture:** each node holds a checklist of everyone downstream (and itself). A node’s checklist is “me” plus the union of checklists of everyone I point to.

**How that becomes the method**

1. Build adjacency list for the DAG.
2. `bitset<N> reach[n+1]`; `bool done[n+1]`.
3. DFS/`dp(v)`: if already computed, return; else set `reach[v][v] = 1`; for each `v→u`, compute `dp(u)` then `reach[v] |= reach[u]`; mark done.
4. For each `v`, print `reach[v].count()`.

Memory: `n * n` bits ≈ `5e4^2 / 8` ≈ 300MB — within 512MB if careful (or use a vector of bitsets). Time is roughly `O(n(n+m)/w)` for the ORs — intended.

**Data structures:** `vector<int> adj[n+1]`; `vector<bitset<N>> reach` or static array; recursion stack / explicit topo order.

## Walkthrough (on a sample)

Node `5` only itself → `1`. Node `3` reaches `3,5` → `2`. Node `1` reaches everyone → `5`.

## Pitfalls

- Treating the graph as possibly cyclic (statement says DAG).  
- Recomputing without memo → exponential blow-up.  
- Memory blow-up if you allocate poorly.  
- Forgetting to count the node itself.

## Complexity

- Time: `O(n(n + m) / w)` bitset ORs  
- Space: `O(n^2 / w)`

## Key takeaway

**DAG reachability counts = DP of bitsets with OR along edges**, then `popcount` per node.
