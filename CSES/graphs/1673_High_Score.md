# [1673 — High Score](https://cses.fi/problemset/task/1673)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, Bellman-Ford, positive cycle  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You play a game consisting of `n` rooms and `m` tunnels. Your initial score is `0`, and each tunnel increases your score by `x` where `x` may be both positive or negative. You may go through a tunnel several times.

Your task is to walk from room `1` to room `n`. What is the maximum score you can get?

### Input

The first input line has two integers `n` and `m`: the number of rooms and tunnels. The rooms are numbered `1, 2, ..., n`.

Then, there are `m` lines describing the tunnels. Each line has three integers `a`, `b` and `x`: the tunnel starts at room `a`, ends at room `b`, and it increases your score by `x`. All tunnels are one-way tunnels.

You can assume that it is possible to get from room `1` to room `n`.

### Output

Print one integer: the maximum score you can get. However, if you can get an arbitrarily large score, print `-1`.

### Constraints

- `1 <= n <= 2500`
- `1 <= m <= 5000`
- `1 <= a, b <= n`
- `-1e9 <= x <= 1e9`

### Examples

**Input**
```
4 5
1 2 3
2 4 -1
1 3 -2
3 4 7
1 4 4
```

**Output**
```
5
```

---

## What this problem really is

Directed graph with (possibly negative) edge weights. Maximize path score from `1` to `n` (edges may be reused). If a **positive** cycle is reachable from `1` and can reach `n`, the score is unbounded → print `-1`.

## What to use

| Tool | Why |
|------|-----|
| Negate weights + Bellman-Ford | Max path ↔ min path on negated edges |
| Extra BF iterations / cycle mark | Detect positive cycles on the original (negative on negated) that touch the `1→n` route |
| Reachability to `n` | Only cycles that can still reach `n` make the answer unbounded |
| `long long` | Large magnitudes |

## Approach

**Root idea:** maximizing score with possible negatives = shortest path on **negated** weights. Unbounded max score ↔ a negative cycle in the negated graph that lies on some walk from `1` to `n`.

**Mental picture:** tunnels add points. A loop that nets positive points, reachable before finishing at `n`, can be farmed forever.

**Data structures**

- Edge list `{a, b, x}` and also negated `{a, b, -x}` for BF.
- `dist[n+1]` for Bellman-Ford from `1` on negated weights.
- Optional: reverse graph to mark nodes that can reach `n`.

**Steps (standard pattern)**

1. Build edge list; negate scores for shortest-path view.
2. `dist[1]=0`, others `INF`. Relax all edges `n-1` times (Bellman-Ford).
3. Record `best = -dist[n]` as candidate finite answer (if `dist[n]` finite).
4. Mark nodes improved on further relaxations → lie on/after a negative cycle (negated) / positive cycle (original).
5. If any such node can reach `n` (and is reachable from `1`), answer `-1`.
6. Else print the finite maximum.

(Equivalently: run BF maximizing with positive edges carefully; same cycle logic.)

## Walkthrough (on a sample)

Paths to `4`: `1→2→4` score `2`; `1→3→4` score `5`; `1→4` score `4`. Best finite **`5`**. No useful positive cycle toward `n`.

## Pitfalls

- Detecting **any** positive cycle in the graph is wrong — only those that can appear on a walk `1 ↝ cycle ↝ n`.
- Forgetting negation when using “shortest path” code.
- `INF` handling / overflow with `±1e9` weights.

## Complexity

- Time: `O(n * m)` Bellman-Ford  
- Space: `O(n + m)`

## Key takeaway

**Max score with +/- edges = shortest path on negated weights.** Unbounded iff a positive cycle intersects some `1→n` walk → print `-1`.
