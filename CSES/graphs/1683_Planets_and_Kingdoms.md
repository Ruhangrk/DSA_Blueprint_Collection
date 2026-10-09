# [1683 — Planets and Kingdoms](https://cses.fi/problemset/task/1683)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, strongly connected components  
**Difficulty / Rating:** Medium–Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A game has `n` planets, connected by `m` teleporters. Two planets `a` and `b` belong to the same kingdom exactly when there is a route both from `a` to `b` and from `b` to `a`. Your task is to determine for each planet its kingdom.

### Input

The first input line has two integers `n` and `m`: the number of planets and teleporters. The planets are numbered `1, 2, ..., n`.

After this, there are `m` lines describing the teleporters. Each line has two integers `a` and `b`: you can travel from planet `a` to planet `b` through a teleporter.

### Output

First print an integer `k`: the number of kingdoms. After this, print for each planet a kingdom label between `1` and `k`. You can print any valid solution.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 6
1 2
2 3
3 1
3 4
4 5
5 4
```

**Output**
```
2
1 1 1 2 2
```

---

## What this problem really is

Directed graph: partition nodes into **strongly connected components** (SCCs). Label each planet by its SCC id; `k` = number of SCCs.

## What to use

| Tool | Why |
|------|-----|
| Kosaraju (2 DFS) or Tarjan | Standard SCC algorithms, linear time |
| Forward + reverse graphs | Kosaraju’s second pass |
| `comp[u]` labels | Output kingdom ids |

## Approach

**Root idea:** mutual reachability is the definition of an SCC. Compute SCCs and assign each a kingdom number.

**Kosaraju sketch**

1. DFS on the original graph; push nodes onto a stack in **finish** order.
2. Transpose the graph (reverse edges).
3. Pop nodes from the stack; DFS on the transpose, each tree = one SCC; assign the same label to all nodes in that DFS.

**Mental picture:** collapse every “you can tour freely among these planets” blob into one kingdom.

**Data structures**

- `g`, `gr`, `vis`, stack/order, `comp[n+1]`, `k`

**Steps**

1. Build digraph and reverse.
2. Run Kosaraju (or Tarjan).
3. Print `k`, then `comp[1] .. comp[n]` (labels `1..k`).

## Walkthrough (on a sample)

`{1,2,3}` mutually reachable; `{4,5}` mutually reachable; no way back from `{4,5}` to `{1,2,3}`. → **`k = 2`**, labels like `1 1 1 2 2`.

## Pitfalls

- Weakly connected components (ignore direction) — wrong kingdoms.
- 0-based vs 1-based labels in output (must be `1..k`).
- Isolated nodes each form their own kingdom.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Kingdoms = SCCs.** Kosaraju/Tarjan label each node; print component count and ids.
