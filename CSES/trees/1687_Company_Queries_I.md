# [1687 — Company Queries I](https://cses.fi/problemset/task/1687)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** binary lifting, ancestor jumps  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A company has `n` employees, who form a tree hierarchy where each employee has a boss, except for the general director.

Your task is to process `q` queries of the form: who is employee `x`'s boss `k` levels higher up in the hierarchy?

### Input

The first input line has two integers `n` and `q`: the number of employees and queries. The employees are numbered `1, 2, ..., n`, and employee `1` is the general director.

The next line has `n-1` integers `e2, e3, ..., en`: for each employee `2, 3, ..., n` their boss.

Finally, there are `q` lines describing the queries. Each line has two integers `x` and `k`: who is employee `x`'s boss `k` levels higher up?

### Output

Print the answer for each query. If such a boss does not exist, print `-1`.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= ei <= i-1`
- `1 <= x <= n`
- `1 <= k <= n`

### Examples

**Input**
```
5 3
1 1 3 3
4 1
4 2
4 3
```

**Output**
```
3
1
-1
```

---

## What this problem really is

Rooted tree (boss pointers). Answer “`k`-th ancestor of `x`” (or `-1` if depth too small). Many queries → need `O(log n)` per query.

## What to use

| Tool | Why |
|------|-----|
| Binary lifting `up[j][v]` | `2^j`-th ancestor of `v` |
| Depth array | Reject when `k > depth[x]` |
| Bit walk on `k` | Jump by powers of two |

## Approach

**Root idea:** precompute for every node its ancestor `1, 2, 4, 8, ...` steps up. To go up `k`, for each bit set in `k`, jump by that power.

**Mental picture:** express the climb as a sum of doubling hops — same idea as binary exponentiation, but on parent pointers.

**Data structures**

- `parent[0][v]` = direct boss (`0` or none for root).
- `parent[j][v] = parent[j-1][parent[j-1][v]]`.
- `depth[v]` from a DFS/BFS.

**Steps**

1. Build children from boss list; DFS depths; fill lifting table (`LOG ~ 18`).
2. For query `(x,k)`: if `k > depth[x]` print `-1`; else for `j = LOG..0`, if `k & (1<<j)` set `x = parent[j][x]`; print `x`.

## Walkthrough (on a sample)

Boss chain: `4 → 3 → 1`. Query `4,1` → **3**; `4,2` → **1**; `4,3` → beyond root → **-1**.

## Pitfalls

- Not handling missing ancestors (`0` in the table).
- Off-by-one: `k` levels higher means `k` parent steps.
- Rebuilding ancestors per query in `O(k)` — TLE.

## Complexity

- Time: `O((n+q) log n)`  
- Space: `O(n log n)`

## Key takeaway

**`k`-th boss = binary lifting** — precompute `2^j` ancestors, walk bits of `k`.
