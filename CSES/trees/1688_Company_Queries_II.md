# [1688 — Company Queries II](https://cses.fi/problemset/task/1688)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** LCA, binary lifting  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A company has `n` employees, who form a tree hierarchy where each employee has a boss, except for the general director.

Your task is to process `q` queries of the form: who is the lowest common boss of employees `a` and `b` in the hierarchy?

### Input

The first input line has two integers `n` and `q`: the number of employees and queries. The employees are numbered `1, 2, ..., n`, and employee `1` is the general director.

The next line has `n-1` integers `e2, e3, ..., en`: for each employee `2, 3, ..., n` their boss.

Finally, there are `q` lines describing the queries. Each line has two integers `a` and `b`: who is the lowest common boss of employees `a` and `b`?

### Output

Print the answer for each query.

### Constraints

- `1 <= n, q <= 2e5`
- `1 <= ei <= i-1`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 3
1 1 3 3
4 5
2 5
1 4
```

**Output**
```
3
1
1
```

---

## What this problem really is

**LCA** (lowest common ancestor) in a rooted tree, many queries. “Lowest common boss” = LCA (if `a` is ancestor of `b`, LCA is `a` itself — still a valid boss of both in the hierarchy sense).

## What to use

| Tool | Why |
|------|-----|
| Binary lifting LCA | `O(n log n)` preprocess, `O(log n)` query |
| Depth + `2^j` ancestors | Lift deeper node, then lift both together |
| Root = `1` | General director |

## Approach

**Root idea:** bring `a` and `b` to the same depth by lifting the deeper one, then lift both in sync until their parents match; the meeting point is the LCA.

**Mental picture:** two employees walk up toward the CEO; the first office both paths share is the answer.

**Steps (binary lifting LCA)**

1. Build tree; DFS `depth` and `parent[0][v]`; fill `parent[j][v]`.
2. `lca(a,b)`:
   - If `depth[a] < depth[b]` swap.
   - Lift `a` up by `depth[a]-depth[b]`.
   - If `a == b`, return `a`.
   - For `j = LOG..0`: if `parent[j][a] != parent[j][b]`, lift both by `2^j`.
   - Return `parent[0][a]`.
3. Answer each query with `lca(a,b)`.

## Walkthrough (on a sample)

`4` and `5` both under `3` → LCA **3**.

`2` and `5`: paths meet at **1**.

`1` and `4`: LCA **1**.

## Pitfalls

- Forgetting the case when one node is ancestor of the other.
- Lifting past the root (store `0` carefully).
- Euler-tour + RMQ is fine too — same asymptotics, different code shape.

## Complexity

- Time: `O((n+q) log n)`  
- Space: `O(n log n)`

## Key takeaway

**Lowest common boss = LCA via binary lifting** — equalize depths, then joint jump until just below the LCA.
