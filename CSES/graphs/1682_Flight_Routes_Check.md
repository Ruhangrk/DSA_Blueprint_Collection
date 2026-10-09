# [1682 — Flight Routes Check](https://cses.fi/problemset/task/1682)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, strongly connected components, reachability  
**Difficulty / Rating:** Medium–Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `m` flight connections. Your task is to check if you can travel from any city to any other city using the available flights.

### Input

The first input line has two integers `n` and `m`: the number of cities and flights. The cities are numbered `1, 2, ..., n`.

After this, there are `m` lines describing the flights. Each line has two integers `a` and `b`: there is a flight from city `a` to city `b`. All flights are one-way flights.

### Output

Print `"YES"` if all routes are possible, and `"NO"` otherwise. In the latter case also print two cities `a` and `b` such that you cannot travel from city `a` to city `b`. If there are several possible solutions, you can print any of them.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

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
NO
4 2
```

---

## What this problem really is

Directed graph: is it **strongly connected**? If not, give a witness pair `(a,b)` with no path `a → b`.

## What to use

| Tool | Why |
|------|-----|
| Kosaraju / Tarjan SCC | Strong connectivity ↔ exactly one SCC (for `n` nodes all together) |
| Or: DFS from `1` + DFS on reversed graph from `1` | Equivalent check when asking “one SCC containing everyone” |
| Reverse adjacency list | Second reachability pass |

## Approach

**Root idea:** you can go from every city to every other iff the graph is one strongly connected component.

**Practical check (often used on CSES):**

1. DFS/BFS from city `1` on the **original** graph — every node must be reachable.
2. DFS/BFS from city `1` on the **reversed** graph — every node must reach `1` (i.e. be reachable from `1` in reverse).
3. If both succeed → `YES`.
4. If some `x` fails forward → cannot go `1 → x` (or find any unreachable); print `NO` and a pair. If forward OK but reverse fails for `y`, then cannot go `y → 1`; print e.g. `y 1`.

(Full SCC condensation also works: need a single SCC.)

**Data structures**

- `g`, `gr` (reversed)
- `vis` arrays for two searches

**Steps**

1. Build forward and reverse graphs.
2. Run the two reachability checks from `1`.
3. Output `YES` or `NO` with a counterexample pair.

## Walkthrough (on a sample)

`1,2,3` form a cycle; `4` is reachable from them but cannot return. So not strongly connected — e.g. **`4 2`** has no path.

## Pitfalls

- Checking only undirected connectivity — ignores direction.
- Only one DFS from `1` forward — misses “cannot return.”
- Counterexample must be ordered `(from, to)` with no route from→to.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**All-pairs directed travel ↔ one SCC.** Verify reachability from `1` and to `1` (via reverse graph), or compute SCCs and demand a single component.
