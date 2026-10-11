# [3111 — Transfer Speeds Sum](https://cses.fi/problemset/task/3111)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** tree, DSU, MST dual, pairwise min-edge  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A computer network has `n` computers and `n-1` connections between two computers. Information can be exchanged between every pair of computers using the connections.

Each connection has a certain transfer speed. Let `d(a,b)` denote the transfer speed between computers `a` and `b`, which is the speed of the slowest connection on the route between `a` and `b`. Your task is to compute the sum of transfer speeds between all pairs of computers.

### Input

The first line contains the integer `n`: the number of computers. The computers are numbered `1,2,...,n`.

After this, there are `n-1` lines, which describe the connections. Each line has three integers `a`, `b` and `x`: there is a connection between computers `a` and `b` with transfer speed `x`.

### Output

Print one integer: the sum of transfer speeds.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x <= 1e6`

### Examples

**Input**
```
4
1 2 5
2 3 1
2 4 2
```

**Output**
```
12
```

Explanation: `d(1,2)=5`, `d(1,3)=1`, `d(1,4)=2`, `d(2,3)=1`, `d(2,4)=2`, and `d(3,4)=1`, so the sum is `12`.

---

## What this problem really is

On a tree, for every pair sum the minimum edge weight on the unique path — total over all `C(n,2)` pairs.

## What to use

| Tool | Why |
|------|-----|
| Sort edges descending | Bottleneck `w` is “paid” by pairs whose path’s minimum is exactly `w` |
| DSU / Union-Find | When connecting by edge `w`, all cross pairs get bottleneck `>= w`, and for the newly merged cut the min is exactly `w` |
| 64-bit ints | Sum can be large (`n=2e5`, `x=1e6`) |

## Approach

**Root idea:** the bottleneck of a pair is the lightest edge on its path. Process edges from heaviest to lightest; when you unite two components by an edge of weight `w`, every pair with one end in each component has bottleneck exactly `w` (heavier edges were already united inside components; this edge is the first / lightest link between them in reverse order…).

**Mental picture (Kruskal in reverse):** start with `n` isolated computers. Add the fastest links first. The moment two clusters join by speed `w`, every pair across the cut “sees” `w` as its slowest link (all faster links are already inside clusters).

**How that becomes the method**

1. Read all tree edges `(a,b,w)`.
2. Sort by `w` **descending**.
3. DSU with component sizes; `ans = 0`.
4. For each edge in that order: if `find(a) != find(b)`,  
   `ans += w * size[A] * size[B]` (use 64-bit), then `unite`.
5. Print `ans`.

**Data structures:** edge list; DSU parent + size arrays; `long long` accumulator.

## Walkthrough (on a sample)

Edges sorted desc: `(1-2,5)`, `(2-4,2)`, `(2-3,1)`.

- Unite `1-2` with `5`: `5 * 1 * 1 = 5`. Components `{1,2}`, `{3}`, `{4}`.
- Unite `2-4` with `2`: `2 * 2 * 1 = 4`. Components `{1,2,4}`, `{3}`.
- Unite `2-3` with `1`: `1 * 3 * 1 = 3`.
- Total `5+4+3 = 12`.

## Pitfalls

- Sorting ascending (wrong — that is a different bottleneck count).
- 32-bit overflow on `w * szA * szB`.
- Not a general graph — input is a tree (`n-1` edges); DSU still works.

## Complexity

- Time: `O(n log n)` for sort + almost-`O(n)` DSU
- Space: `O(n)`

## Key takeaway

Sum of path-minima on a tree = process edges heavy-to-light with DSU; each unite by `w` adds `w * szA * szB`.
