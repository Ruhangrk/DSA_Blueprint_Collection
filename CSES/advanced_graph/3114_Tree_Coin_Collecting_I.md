# [3114 — Tree Coin Collecting I](https://cses.fi/problemset/task/3114)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** tree, LCA, multi-source BFS, path minimum  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a tree with `n` nodes. Some nodes contain a coin.

Your task is to answer `q` queries of the form: what is the shortest length of a path from node `a` to node `b` that visits a node with a coin?

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
2
3
0
4
```

---

## What this problem really is

For each query `(a,b)`, minimize walk length from `a` to `b` that touches at least one coin node — i.e. `min_over_coins c of dist(a,c) + dist(c,b)` (a closed detour when `a=b`).

## What to use

| Tool | Why |
|------|-----|
| Multi-source BFS from all coins | `d[v]` = distance from `v` to nearest coin |
| Path identity | `min_c dist(a,c)+dist(b,c) = dist(a,b) + 2 * min_{v on path(a,b)} d[v]` |
| LCA + binary lifting mins | Query `dist` and path-minimum of `d` in `O(log n)` |

## Approach

**Root idea:** an optimal coin is attached to the `a-b` path at the path vertex closest to some coin. That attachment cost is `min_{v on path} d[v]`, paid twice (leave the path and return), on top of walking `a` to `b`.

**Mental picture:** walk along `a…b`. At the best “exit ramp” `v`, drop down to a coin (distance `d[v]`) and climb back, then continue. If a coin already lies on the road, the ramp length is `0`.

**How that becomes the method**

1. Multi-source BFS: push every coin at distance `0`; compute `d[1..n]`.
2. Root the tree; build LCA + lifting for depth/dist and for `minD` on jumps (`min d` along the lifted edges/nodes).
3. For query `(a,b)`:  
   `ans = dist(a,b) + 2 * pathMinD(a,b)`.
4. Sanity: if `a=b` and `a` has a coin, `d[a]=0` → `ans=0`; if not, `ans = 2*d[a]`.

**Data structures:** adj; queue for BFS; `up[LOG][n]`, `mn[LOG][n]`; depth array.

## Walkthrough (on a sample)

Coins at `1,4`. `d = [?,0,1,1,0,2]` for nodes `1..5` (approx: `d[1]=0,d[2]=1,d[3]=1,d[4]=0,d[5]=2`).

- `(1,5)`: path `1-3-5`, `min d = 0` → `2 + 0 = 2`.
- `(3,2)`: path `3-2`, `min d = 1` → `1 + 2 = 3`.
- `(4,4)`: `0 + 2*0 = 0`.
- `(5,5)`: `0 + 2*2 = 4`.

## Pitfalls

- Answering `dist(a,b)` alone when the path misses all coins.
- Using only `d[a]+d[b]` (wrong).
- `O(n)` path scans per query → TLE; need lifting for path minima.

## Complexity

- Time: `O((n + q) log n)`
- Space: `O(n log n)`

## Key takeaway

Shortest `a↝coin↝b` walk on a tree = `dist(a,b) + 2 * (min nearest-coin distance among vertices on the path)`.
