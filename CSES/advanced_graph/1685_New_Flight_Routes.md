# [1685 — New Flight Routes](https://cses.fi/problemset/task/1685)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** SCC, condensation DAG, strong connectivity  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `m` flight connections between them. Your task is to add new flights so that it will be possible to travel from any city to any other city. What is the minimum number of new flights required?

### Input

The first input line has two integers `n` and `m`: the number of cities and flights. The cities are numbered `1,2,...,n`.

After this, there are `m` lines describing the flights. Each line has two integers `a` and `b`: there is a flight from city `a` to city `b`. All flights are one-way flights.

### Output

First print an integer `k`: the required number of new flights. After this, print `k` lines describing the new flights. You can print any valid solution.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a,b <= n`

### Examples

**Input**
```
4 5
1 2
2 3
3 1
1 4
3 4
```

**Output**
```
1
4 2
```

---

## What this problem really is

Add the fewest directed edges to make a digraph **strongly connected**, and construct them.

## What to use

| Tool | Why |
|------|-----|
| SCC condensation | Strong connectivity reduces to linking a DAG |
| Sources / sinks | Condensation nodes with indegree 0 / outdegree 0 |
| Pairing construction | `k = max(|sources|, |sinks|)` edges suffice and are necessary |

## Approach

**Root idea:** after contracting SCCs, you get a DAG. Making the original graph strong = making this DAG strong by adding edges between SCCs (then lift to real cities).

**Mental picture:** SCC blobs with arrows only forward. Sources have no way in; sinks have no way out. Wire sink → source in a cycle covering all lonely ends.

**How that becomes the method**

1. Kosaraju/Tarjan → SCCs; if only one SCC, answer `0` (already strong). Edge case: `n=1` → `0`.
2. Build condensation DAG; compute indegree/outdegree of each SCC.
3. Let `S` = SCCs with indegree `0`, `T` = SCCs with outdegree `0`.
4. `k = max(|S|, |T|)`.
5. Construct: pick representatives (any city) from each source/sink SCC. Pair them so each new edge goes from a sink rep to a source rep, chaining all of `S` and `T` into one loop (standard: sort sources/sinks, connect `T[i] → S[i]` cyclically, then attach leftovers). This uses exactly `k` edges.
6. Print `k` and the city-level edges.

**Data structures:** adj + radj; SCC id array; condensation deg; vectors of source/sink reps.

## Walkthrough (on a sample)

SCCs: `{1,2,3}` and `{4}`. Edge `{1,2,3} → {4}`.  
Sources: `{1,2,3}` (indeg 0). Sinks: `{4}` (outdeg 0).  
`k = max(1,1) = 1`. Add `4 → 2` (sink → source). Now fully strong.

## Pitfalls

- Forgetting the single-SCC → `0` case.
- Isolated nodes are their own SCCs (both source and sink).
- Adding edges inside an SCC (useless for this task).

## Complexity

- Time: `O(n + m)`
- Space: `O(n + m)`

## Key takeaway

Min edges to strong-connect a digraph = `max(#source SCCs, #sink SCCs)` on the condensation (0 if already one SCC), with a sink→source pairing construction.
