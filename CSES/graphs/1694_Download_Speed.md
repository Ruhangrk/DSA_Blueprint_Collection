# [1694 — Download Speed](https://cses.fi/problemset/task/1694)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, max flow, Dinic/Edmonds-Karp  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a network consisting of `n` computers and `m` connections. Each connection specifies how fast a computer can send data to another computer.

Kotivalo wants to download some data from a server. What is the maximum speed he can do this, using the connections in the network?

### Input

The first input line has two integers `n` and `m`: the number of computers and connections. The computers are numbered `1, 2, ..., n`. Computer `1` is the server and computer `n` is Kotivalo's computer.

After this, there are `m` lines describing the connections. Each line has three integers `a`, `b` and `c`: computer `a` can send data to computer `b` at speed `c`.

### Output

Print one integer: the maximum speed Kotivalo can download data.

### Constraints

- `1 <= n <= 500`
- `1 <= m <= 1000`
- `1 <= a, b <= n`
- `1 <= c <= 1e9`

### Examples

**Input**
```
4 5
1 2 3
2 4 2
1 3 4
3 4 5
4 1 3
```

**Output**
```
6
```

---

## What this problem really is

Directed network with capacities `c`. **Maximum flow** from source `1` to sink `n` (edge capacities = speeds; flow can split/merge).

## What to use

| Tool | Why |
|------|-----|
| Flow network residual graph | Standard max-flow setup |
| Dinic or Edmonds-Karp | `n<=500`, `m<=1000` — Dinic preferred for speed |
| `long long` | Capacities up to `1e9`, flow can sum large |

## Approach

**Root idea:** download speed = how much “data per unit time” can be pushed from server to Kotivalo under link capacity limits → max flow.

**Mental picture:** pipes with bandwidths; max simultaneous throughput from `1` to `n`.

**Data structures**

- Residual adjacency (forward capacity, reverse edge index).
- Level graph + DFS blocking flow (Dinic), or BFS augmenting paths (EK).

**Steps**

1. Build residual graph; for each connection `a→b` capacity `c` (multiple edges: sum capacities).
2. Compute `maxflow(1, n)`.
3. Print the flow value.

## Walkthrough (on a sample)

Paths `1→2→4` (cap limited by `2`) and `1→3→4` (cap `4`) combine to throughput **`6`**.

## Pitfalls

- Treating as shortest path / MST — wrong problem.
- `int` overflow on flow sum.
- Forgetting residual reverse edges.

## Complexity

- Dinic: fine for these limits; EK `O(m^2 F)` can be tight with large capacities — use scaling or Dinic.

## Key takeaway

**Max download speed through capacity links = max flow** from server to client.
