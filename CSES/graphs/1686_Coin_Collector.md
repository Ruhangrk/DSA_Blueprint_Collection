# [1686 — Coin Collector](https://cses.fi/problemset/task/1686)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, SCC, DAG DP  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A game has `n` rooms and `m` tunnels between them. Each room has a certain number of coins. What is the maximum number of coins you can collect while moving through the tunnels when you can freely choose your starting and ending room?

### Input

The first input line has two integers `n` and `m`: the number of rooms and tunnels. The rooms are numbered `1, 2, ..., n`.

Then, there are `n` integers `k1, k2, ..., kn`: the number of coins in each room.

Finally, there are `m` lines describing the tunnels. Each line has two integers `a` and `b`: there is a tunnel from room `a` to room `b`. Each tunnel is a one-way tunnel.

### Output

Print one integer: the maximum number of coins you can collect.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= ki <= 1e9`
- `1 <= a, b <= n`

### Examples

**Input**
```
4 4
4 5 2 7
1 2
2 1
1 3
2 4
```

**Output**
```
16
```

---

## What this problem really is

Directed graph with node weights. Max sum of coins on a walk (start/end free). You may visit rooms multiple times, but coins in a room are collected once (standard interpretation: take each room’s coins at most once — equivalent to taking all coins in an SCC when you enter that component).

## What to use

| Tool | Why |
|------|-----|
| SCC condensation | Inside an SCC you can tour freely → take **all** coins in that SCC once |
| Condensation DAG | Edges between components; no cycles |
| DP on DAG | `dp[comp] = coins[comp] + max(0 or dp[neighbors])` style for best path sum |

## Approach

**Root idea:** collapsing each SCC into a supernode with weight = sum of room coins. The problem reduces to a **heaviest path** on the resulting DAG (pick best start/end supernodes).

**Mental picture:** tightly looped rooms are one treasure chest; then you only move downhill on the component DAG picking chests.

**Data structures**

- Digraph; Kosaraju/Tarjan → `comp[u]`, `sum[c]`
- Condensation edges (dedupe)
- `dp[c]` = max coins collectable starting from component `c` (including `sum[c]`), computed in reverse topo / memo DFS

**Steps**

1. Compute SCCs; `sum[c] = sum of k[u]` for `u` in `c`.
2. Build DAG of components.
3. DP: `dp[c] = sum[c] + max(0, max over edges c→d of dp[d])` — or if you must only follow edges, `dp[c] = sum[c] + max(dp[d] or 0 if no outgoing)`.
4. Answer = `max dp[c]` over all components.

## Walkthrough (on a sample)

Rooms `1,2` in one SCC with coins `4+5=9`; then to `3` (`2`) or `4` (`7`). Best path takes `{1,2}` and `4` → `9+7 = 16`.

## Pitfalls

- Collecting a room’s coins multiple times — SCC sum once is the model.
- DP on the original graph with cycles without SCC — infinite if positive cycle of revisits; SCC avoids that.
- `long long` for sums.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Max score with cycles = SCC compress, then longest/heaviest path DP on the DAG** of component coin sums.
