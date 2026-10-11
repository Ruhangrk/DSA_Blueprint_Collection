# [1706E — Qpwoeirut and Vertices](https://codeforces.com/problemset/problem/1706/E)

**Platform:** Codeforces
**Source:** Codeforces Round 809 (Div. 2)
**Tags:** binary search, data structures, dfs and similar, divide and conquer, dsu, greedy, trees
**Difficulty / Rating:** 2300
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Codeforces Round #809 Editorial](https://codeforces.com/blog/entry/105008)

---

## Problem Statement

You are given a connected undirected graph with `n` vertices and `m` edges. Vertices are numbered `1` to `n`, and edges are numbered `1` to `m`.

You must answer `q` queries `(l, r)`. The answer to each query is the smallest non-negative integer `k` such that every pair of vertices `a`, `b` with `l <= a <= b <= r` is connected using only edges `1..k`.

### Input

The first line contains `t` (`1 <= t <= 1000`). Each test case starts with `n`, `m`, `q` (`2 <= n <= 1e5`, `1 <= m, q <= 2e5`). Then `m` lines `u v` describe the edges. The graph is connected with no multiple edges and no self-loops. Then `q` lines `l r` (`1 <= l <= r <= n`).

The sums of `n` over all tests are at most `1e5`, and sums of `m` and `q` are at most `2e5` each.

### Output

For each test case, print the `q` answers on one line.

### Constraints

- `1 <= t <= 1000`
- `2 <= n <= 1e5`
- `1 <= m, q <= 2e5`
- Sums of `n` at most `1e5`; sums of `m` and `q` at most `2e5`

### Examples

Input:

```
3
2 1 2
1 2
1 1
1 2
5 5 5
1 2
1 3
2 4
3 4
3 5
1 4
3 4
2 2
2 5
3 5
3 2 1
1 3
2 3
1 3
```

Output:

```
0 1 
3 3 0 5 5 
2 
```

### Note

In the second test, vertices `1..4` need edges `1, 2, 3` to be connected, so the first query answers `3`. Query `(2, 2)` answers `0` because a vertex is trivially connected to itself.

---

## What this problem really is

For a query `[l, r]`, all vertices in the range must be mutually connected using the first `k` edges. Connectivity of the whole range is implied by connectivity of each consecutive pair `(i - 1, i)` for `i` in `l+1..r`. So the problem reduces to a pair-level question, and then a range maximum.

## What to use

| Tool | Why |
|------|-----|
| Define `f(i)` = smallest `k` that connects vertices `i-1` and `i` | Turns each query into a range over single pairs. |
| Kruskal in edge-index order (DSU) | Builds the minimum spanning tree where weight equals index. The MST path between two vertices has the smallest possible maximum index. |
| DSU-based tree with edge labels | Lets us read the max index on the MST path between `i-1` and `i`. |
| Range maximum query (sparse table or segment tree) | Answers each query as `max f(l+1..r)`. |

## Approach

**Root idea:** For `l < r`, the answer is `max(f(l+1), ..., f(r))`, where `f(i)` is the answer for the pair `(i-1, i)`. A query with `l = r` answers `0`.

Why the max works: the first `k` edges connect the whole range exactly when they connect every consecutive pair. If the largest `f(i)` in the range is `K`, then edges `1..K` connect every consecutive pair, so the whole range is connected. Any smaller `k` leaves the pair achieving `K` disconnected, so `K` is the minimum.

How to get `f(i)`: Weight edge `j` by `j`. The smallest `k` that connects `u` and `v` is the largest weight on the path between them in the minimum spanning tree (MST). Kruskal processes edges in index order, so the MST is exactly the one built by DSU as edges are added in order.

**Mental picture:** Build a family tree of components. Each time two components merge at edge `j`, add a link labeled `j` between their roots. Labels grow as you go up, so the max label on the path from `i-1` to `i` is the highest link between them.

**How that becomes the method:**
1. Read the graph. Process edges `1..m` in order with a DSU. For each edge that merges two components, record a tree link between their roots, labeled with the edge index. Edges within one component are skipped.
2. For each `i` from `2` to `n`, compute `f(i)` as the maximum label on the tree path from `i-1` to `i`. Walk up from both vertices to their LCA, taking the largest label seen. The editorial bounds this walk by `O(log n)` with small-to-large merging.
3. Build a range maximum structure over `f(2..n)`.
4. For each query `(l, r)`: output `0` if `l == r`, otherwise output the range maximum of `f(l+1..r)`.

**Data structures:**
- DSU with union by size (or rank), where each root keeps a link to its tree parent and the label.
- Tree arrays for parent links and labels.
- Sparse table or segment tree over `f`.

## Walkthrough (on a sample)

Test 2: `n = 5`, `m = 5`, edges in order: `1: 1-2`, `2: 1-3`, `3: 2-4`, `4: 3-4`, `5: 3-5`.

Kruskal by index: edges 1, 2, 3 merge components (`1-2`, then `1-3`, then `2-4`). Edge 4 (`3-4`) joins already-connected vertices and is skipped. Edge 5 (`3-5`) merges vertex `5`.

Pair values:

- `f(2)` for pair `(1, 2)`: path `1-2` uses edge 1, so `f(2) = 1`.
- `f(3)` for pair `(2, 3)`: path `2-1-3` uses edges 1 and 2, so `f(3) = 2`.
- `f(4)` for pair `(3, 4)`: MST path `3-1-2-4` uses edges 2, 1, 3, so `f(4) = 3`.
- `f(5)` for pair `(4, 5)`: MST path `4-2-1-3-5` uses edges 3, 1, 2, 5, so `f(5) = 5`.

Query `(1, 4)`: `max(f(2), f(3), f(4)) = max(1, 2, 3) = 3`. Matches.
Query `(2, 5)`: `max(f(3), f(4), f(5)) = max(2, 3, 5) = 5`. Matches.
Query `(3, 4)`: `f(4) = 3`. Matches.
Query `(2, 2)`: `l = r`, answer `0`. Matches.

## Pitfalls

- **Forgetting `l = r`:** The range is empty of pairs, so the answer is `0`, not the maximum of an empty set.
- **Using the MST by weight instead of index:** Here weight equals index, so Kruskal in input order is the correct MST.
- **Skipping edges that don't merge:** Those edges are not in the MST and never change `f`, so skipping them is correct.
- **Using the shortest path instead of the MST path:** The MST is built by index, so the path max is the right quantity. A BFS path does not minimize the max index.
- **Only checking the endpoints:** Connectivity of the range needs every consecutive pair, so take the max over all of `f(l+1..r)`.

## Complexity

- Time: `O(m log n)` for DSU work and the walks, plus `O(n log n)` for the range structure, and `O(1)` per query with a sparse table.
- Space: `O(n log n)` for the sparse table.

## Key takeaway

Reduce a range connectivity query to consecutive pairs, compute each pair's cost as the MST path maximum, and answer queries with a range maximum.
