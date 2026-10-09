# [1723 — Graph Paths I](https://cses.fi/problemset/task/1723)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** matrix exponentiation, adjacency matrix, walk counting  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a directed graph that has `n` nodes and `m` edges. Your task is to count the number of paths from node `1` to node `n` with exactly `k` edges.

### Input

The first input line contains three integers `n`, `m` and `k`: the number of nodes and edges, and the length of the path. The nodes are numbered `1,2,...,n`.

Then, there are `m` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge from node `a` to node `b`.

### Output

Print the number of paths modulo `1e9+7`.

### Constraints

- `1 <= n <= 100`
- `1 <= m <= n*(n-1)`
- `1 <= k <= 1e9`
- `1 <= a,b <= n`

### Examples

**Input**
```
3 4 8
1 2
2 3
3 1
3 2
```

**Output**
```
2
```

Explanation: The paths are `1 -> 2 -> 3 -> 1 -> 2 -> 3 -> 1 -> 2 -> 3` and `1 -> 2 -> 3 -> 2 -> 3 -> 2 -> 3 -> 2 -> 3`.

---

## What this problem really is

Count walks of **exactly** `k` edges from `1` to `n` in a digraph. `k` can be `1e9`, so you cannot BFS/`O(k)` DP on nodes.

## What to use

| Tool | Why |
|------|-----|
| Adjacency matrix `A` | `A[u][v]` = number of edges `u -> v` (multi-edges allowed) |
| Fact: `(A^k)[u][v]` = walks of length `k` from `u` to `v` | Algebraic walk counting |
| Matrix binary exponentiation | Reach power `k` in `O(n^3 log k)` |

## Approach

**Root idea:** one matrix multiply adds one hop. If `C[u][v]` counts walks of length `t` from `u` to `v`, then `(C * A)[u][v] = sum_w C[u][w] * A[w][v]` counts walks of length `t+1`. So `A^k` is exactly the answer table for length `k`.

**Mental picture:** think of `A` as “take one edge.” Squaring is “take two edges,” and so on — same idea as fast pow, but for graphs.

**Steps**

1. Build `n x n` matrix `A`, zero-initialized. For each edge `a -> b`, do `A[a-1][b-1]++` (multi-edges matter).
2. Compute `B = A^k` with modular matrix multiplication (`MOD = 1e9+7`).
3. Print `B[0][n-1]` (node `1` → node `n`).

Identity matrix is the base for binary exponentiation (`k` bits of multiply).

## Walkthrough (on a sample)

`n=3`, edges `1->2`, `2->3`, `3->1`, `3->2`, `k=8`.  
`A^8[0][2] = 2` — the two walks listed in the statement.

## Pitfalls

- Forgetting multi-edges (`++` not `= 1`).
- Paths vs simple paths — walks may revisit nodes; that is intended.
- `O(k)` DP — TLE when `k = 1e9`.
- Wrong indexing (`1`-based vs `0`-based).

## Complexity

- Time: `O(n^3 log k)`  
- Space: `O(n^2)`

## Key takeaway

**Walks of length `k` = entry of `A^k`** — build the adjacency matrix and matrix-exponentiate.
