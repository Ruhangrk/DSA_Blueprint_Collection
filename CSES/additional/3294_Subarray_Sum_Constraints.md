# [3294 — Subarray Sum Constraints](https://cses.fi/problemset/task/3294)

**Platform:** CSES  
**Source:** CSES Problem Set — Additional Problems I  
**Tags:** Prefix sums, weighted union-find (DSU with potentials), consistency checking, constructive  
**Difficulty / Rating:** Not listed on CSES  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Construct an array `x[1], ..., x[n]` of `n` integers satisfying `m` constraints of the form `(l, r, s)`: the sum `x[l] + ... + x[r]` must equal `s`.

### Input

- The first line has two integers `n` and `m`.
- The next `m` lines each have three integers `l`, `r`, `s`.

### Output

If a solution exists, print `YES` on the first line and the array on the second line. Every element must satisfy `-1e15 <= x[i] <= 1e15`, and all constraints must hold. Any valid array is accepted.

If no solution exists, print `NO`.

### Constraints

- `1 <= n <= 5000`
- `0 <= m <= 2e5`
- `1 <= l <= r <= n`
- `-1e9 <= s <= 1e9`

### Examples

**Input**
```
5 3
1 3 3
3 5 3
4 4 -1
```

**Output**
```
YES
0 2 1 -1 3
```

Any valid array is accepted. The array `0 0 3 -1 1` also satisfies all three constraints.

---

## What this problem really is

Each constraint is a sum over a range. Rewrite it using prefix sums `p[i] = x[1] + ... + x[i]`, with `p[0] = 0`. Then `x[l] + ... + x[r] = s` becomes `p[r] - p[l-1] = s`. The problem becomes: assign values to prefix sums `p[0..n]` so that `m` differences are fixed, with `p[0] = 0`. Then check consistency.

## What to use

| Tool | Why |
|------|-----|
| Prefix-sum transformation | Turns range sums into differences of two points |
| Weighted DSU (union-find with potentials) | Keeps `p[v] - p[root]` for each node and merges equal-difference pairs in near-constant time |
| Consistency check during union | Detects contradictions such as `p[a] - p[b] = 3` and `p[a] - p[b] = 4` |
| Free assignment for unconstrained nodes | Any `x` is allowed, so unconnected components can be set freely |

## Approach

**Root idea:** a set of equalities of the form `p[v] - p[u] = w` is consistent iff, when you build the graph with weighted edges, every cycle has total weight zero. A weighted DSU detects this as you add edges: if two nodes are already in the same component, check that their stored offset matches.

**Mental picture:** each prefix node `p[i]` is a point on a number line. A constraint says two points are a fixed distance apart. Points linked by constraints form rigid clusters (they move together, like beads on a rigid rod). Each cluster can be slid to any position. Two clusters with no constraints between them are free relative to each other. Contradiction happens only when a rigid cluster is forced to have two different internal spacings.

**How that becomes the method**

1. Nodes are `0..n` (prefix indices). Each constraint `(l, r, s)` adds an edge `l-1 -> r` with weight `p[r] - p[l-1] = s`.
2. Weighted DSU: `parent[v]` and `pot[v] = p[v] - p[parent[v]]`. `find(v)` returns the root and sets `pot[v]` to `p[v] - p[root]` (path compression updates potentials).
3. To add `p[v] - p[u] = w`: let `(ru, du) = find(u)` with `du = p[u] - p[ru]`, and `(rv, dv) = find(v)` with `dv = p[v] - p[rv]`.
   - If `ru == rv`: check `dv - du == w`. If not, answer `NO`.
   - Else: attach `rv` under `ru`. Need `pot[rv] = p[rv] - p[ru]`. Since `p[v] = p[u] + w`, `p[rv] = p[v] - dv = p[u] + w - dv = p[ru] + du + w - dv`. So `pot[rv] = du + w - dv`.
4. After all unions, for each node `v` compute `p[v] = pot_to_root(v) + value[root]`, with `value[root] = 0`.
5. Output `x[i] = p[i] - p[i-1]` for `i = 1..n`.

**Data structures:**
- `parent[0..n]`, `pot[0..n]` (weighted DSU, `long long`).
- `find` with iterative or recursive path compression that also updates `pot`.

## Walkthrough (on a sample)

Nodes `0..5`. Constraints:

- `(1,3,3)`: edge `0 -> 3`, `p[3] - p[0] = 3`.
- `(3,5,3)`: edge `2 -> 5`, `p[5] - p[2] = 3`.
- `(4,4,-1)`: edge `3 -> 4`, `p[4] - p[3] = -1`.

Start: all nodes separate, `pot = 0`.

1. Add `p[3] - p[0] = 3`. `find(0) = (0, 0)`, `find(3) = (3, 0)`. Different roots. Attach root `3` under `0` with `pot[3] = du + w - dv = 0 + 3 - 0 = 3`. Now `p[3] = p[0] + 3`.
2. Add `p[5] - p[2] = 3`. Attach `5` under `2`, `pot[5] = 0 + 3 - 0 = 3`.
3. Add `p[4] - p[3] = -1`. `find(3) = (0, 3)`, so `du = 3`, root `0`. `find(4) = (4, 0)`, `dv = 0`. Different roots. Attach `4` under `0`: `pot[4] = du + w - dv = 3 + (-1) - 0 = 2`. So `p[4] = p[0] + 2 = 2`.

Values: root `0` has `p = 0`, so `p[0]=0, p[3]=3, p[4]=2`. Root `2` has `p[2]=0` (choose 0), so `p[5]=3`. Node `1` is alone, `p[1]=0`.

`p = [0, 0, 0, 3, 2, 3]`. `x[i] = p[i] - p[i-1]`: `x = [0, 0, 3, -1, 1]`.

Check: `x1+x2+x3 = 3` ✓, `x3+x4+x5 = 3-1+1 = 3` ✓, `x4 = -1` ✓.

A contradictory example: adding `p[3] - p[0] = 4` after the first constraint: `find(3) = (0,3)`, `find(0) = (0,0)`, same root, check `dv - du = 3 - 0 = 3 != 4`, output `NO`.

## Pitfalls

- Mixing up direction: `p[r] - p[l-1] = s`, not `p[l-1] - p[r]`.
- Fixing the wrong anchor. Only differences of prefix sums matter for `x`, so any shift of a whole component is valid. Setting `p[0] = 0` is a convenient choice, not a requirement, but `p[0]` is still just one node in the DSU.
- Forgetting path compression updates the potentials. A `find` that does not update `pot` returns wrong offsets.
- Skipping the consistency check for already-connected nodes. This is the only place contradictions appear.
- Overflow: potentials are sums of up to `m` values up to `1e9`, so up to `2e14`. Use `long long`. The output bound `1e15` is safe.
- `m = 0`: print `YES` and any array (all zeros works).

## Complexity

- Time: `O((n + m) * alpha(n))` with path compression and union by size or rank.
- Space: `O(n)`.

## Key takeaway

Range-sum equalities become difference equalities on prefix sums. A weighted DSU checks them incrementally: rigid clusters are consistent or they are not.
