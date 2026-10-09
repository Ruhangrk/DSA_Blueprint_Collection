# [1751 — Planets Cycles](https://cses.fi/problemset/task/1751)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, functional graph, cycles  
**Difficulty / Rating:** Medium–Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are playing a game consisting of `n` planets. Each planet has a teleporter to another planet (or the planet itself).

You start on a planet and then travel through teleporters until you reach a planet that you have already visited before.

Your task is to calculate for each planet the number of teleportations there would be if you started on that planet.

### Input

The first input line has an integer `n`: the number of planets. The planets are numbered `1, 2, ..., n`.

The second line has `n` integers `t1, t2, ..., tn`: for each planet, the destination of the teleporter. It is possible that `ti = i`.

### Output

Print `n` integers according to the problem statement.

### Constraints

- `1 <= n <= 2e5`
- `1 <= ti <= n`

### Examples

**Input**
```
5
2 4 3 1 4
```

**Output**
```
3 3 1 3 4
```

---

## What this problem really is

Functional graph. From each start, walk until you hit a **repeat**; report the number of teleports in that walk (length until first revisit).

## What to use

| Tool | Why |
|------|-----|
| Cycle detection (color / Floyd on structure) | Each component has one cycle |
| `ans[i]` memo | Answers share structure along tails |
| DFS/iteration over components | `n = 2e5` needs `O(n)` |

## Approach

**Root idea:** the walk from `i` goes along a unique path, then loops. The first repeated planet is the first cycle node you hit again — so:

- If `i` is **on a cycle** of length `L`: you take `L` steps to return to `i` → answer `L`.
- If `i` is on a **tail** of length `d` into a cycle of length `L`: you walk `d` steps onto the cycle, then `L` more until you revisit that entry (or equivalent) → answer `d + L`.

**Mental picture:** fly until you see a familiar planet. On a pure loop that’s the loop length. On a stick into a loop, it’s stick length + loop length.

**Data structures**

- `t[1..n]`
- Mark cycles: iterate with states (unvis / visiting / done), or follow each path with a stack.
- `cyc_len[node]`, `dist_to_cyc[node]`
- `ans[i] = dist_to_cyc[i] + cyc_len[component]`

**Steps**

1. Find all cycles and their lengths; mark cycle nodes.
2. For non-cycle nodes, compute distance along unique edges until a cycle node.
3. For each `i`, output `dist_to_cyc[i] + cycle_length` (with `dist_to_cyc = 0` on cycle).
4. Self-loop → answer `1`.

## Walkthrough (on a sample)

`t = [2,4,3,1,4]` → cycle `1→2→4→1` length `3`; `3→3` length `1`; `5→4` then into cycle.

Answers: from `1/2/4` → `3`; from `3` → `1`; from `5` → `1+3 = 4` → **`3 3 1 3 4`**.

## Pitfalls

- Counting until second visit to **start** only — wrong on tails (first repeat is the cycle entry, not necessarily the start).
- `O(n^2)` simulating each start separately — TLE.
- Self-loops and 2-cycles.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Steps until revisit = distance to cycle + cycle length.** Precompute cycles once; reuse for every starting planet.
