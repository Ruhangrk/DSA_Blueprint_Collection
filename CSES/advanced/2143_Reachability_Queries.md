# [2143 — Reachability Queries](https://cses.fi/problemset/task/2143)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** SCC, condensation, bitset, DAG reachability  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A directed graph consists of `n` nodes and `m` edges. The nodes are numbered `1,2,...,n`.

Your task is to answer `q` queries of the form "can you reach node `b` from node `a`?"

### Input

The first input line has three integers `n`, `m` and `q`: the number of nodes, edges and queries.

Then there are `m` lines describing the edges. Each line has two distinct integers `a` and `b`: there is an edge from node `a` to node `b`.

Finally there are `q` lines describing the queries. Each line consists of two integers `a` and `b`: "can you reach node `b` from node `a`?"

### Output

Print the answer for each query: either `"YES"` or `"NO"`.

### Constraints

- `1 <= n <= 5e4`
- `1 <= m, q <= 1e5`

### Examples

**Input**
```
4 4 3
1 2
2 3
3 1
4 3
1 3
1 4
4 1
```

**Output**
```
YES
NO
YES
```

---

## What this problem really is

Online reachability on a general digraph (may have cycles). `n` and `q` are large, so you need SCC compression plus fast DAG reachability.

## What to use

| Tool | Why |
|------|-----|
| Kosaraju / Tarjan SCC | Inside a component everything reaches everything |
| Condensation DAG | Reachability between components is acyclic |
| Bitset DP on the DAG | Same pattern as Reachable Nodes, then map queries to components |

## Approach

**Root idea:** `a` reaches `b` iff their SCCs are equal, or there is a path from `scc[a]` to `scc[b]` in the condensation DAG.

**Mental picture:** squash every strongly connected blob into a super-node. Queries become “can I walk downhill from this blob to that blob on the DAG of blobs?”

**How that becomes the method**

1. Compute SCCs; assign each node a component id `c[v]`.
2. Build the condensation: edges `c[u] → c[v]` for original edges with different components (dedupe).
3. On that DAG, compute for each component a bitset of reachable component ids (DP with OR, memoized DFS / topo).
4. Query `(a,b)`: `YES` iff `reach[c[a]][c[b]]` is set (same component ⇒ yes).

**Data structures:** SCC arrays; condensation adj; `vector<bitset> reach` over `#components <= n`.

## Walkthrough (on a sample)

`{1,2,3}` form a cycle SCC; `4 → 3`. From `1` to `3`: same / reachable SCC → `YES`. From `1` to `4`: cannot go uphill → `NO`. From `4` to `1`: `4`’s component reaches the cycle → `YES`.

## Pitfalls

- Answering on the raw graph with `n` DFS per query.  
- Forgetting same-SCC ⇒ reachable.  
- Duplicate condensation edges blowing constants.  
- Memory of `n` bitsets of size `n`.

## Complexity

- Time: `O(n + m + n^2/w + q)` roughly for bitset DP  
- Space: `O(n^2 / w)`

## Key takeaway

**Digraph reachability queries = SCC → DAG bitset reachability** on the condensation.
