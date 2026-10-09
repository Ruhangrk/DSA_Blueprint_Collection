# [1681 — Game Routes](https://cses.fi/problemset/task/1681)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, DAG DP, path counting  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A game has `n` levels, connected by `m` teleporters, and your task is to get from level `1` to level `n`. The game has been designed so that there are no directed cycles in the underlying graph. In how many ways can you complete the game?

### Input

The first input line has two integers `n` and `m`: the number of levels and teleporters. The levels are numbered `1, 2, ..., n`.

After this, there are `m` lines describing the teleporters. Each line has two integers `a` and `b`: there is a teleporter from level `a` to level `b`.

### Output

Print one integer: the number of ways you can complete the game. Since the result may be large, print it modulo `1e9+7`.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
4 5
1 2
2 4
1 3
3 4
1 4
```

**Output**
```
3
```

---

## What this problem really is

DAG: count directed paths from `1` to `n`, modulo `1e9+7`.

## What to use

| Tool | Why |
|------|-----|
| Topological order | DAG → safe DP order |
| `ways[u]` | Number of paths from `1` to `u` |
| Modulo `1e9+7` | Can be huge |

## Approach

**Root idea:** every path to `v` enters via some in-edge `u → v`. So `ways[v] = sum of ways[u]` over those predecessors (only after `ways[u]` are final).

**Mental picture:** push path counts forward along a topo order, starting with `ways[1] = 1`.

**Data structures**

- Digraph; Kahn/DFS topo.
- `ways[n+1]`, `ways[1] = 1`, others `0`.

**Steps**

1. Build digraph; compute topo order.
2. For `u` in topo order: for each `u→v`, `ways[v] = (ways[v] + ways[u]) % MOD`.
3. Print `ways[n]`.

(Only nodes reachable from `1` get positive ways; unreachable stay `0`.)

## Walkthrough (on a sample)

Paths: `1-2-4`, `1-3-4`, `1-4` → **`3`**.

## Pitfalls

- Processing edges in arbitrary order without topo — wrong on DAGs with multiple layers.
- Forgetting modulo.
- Cycles are guaranteed absent — don’t need cycle checks.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Path count on a DAG:** `ways[v] = sum ways[u]` over in-edges, computed in topological order from the source.
