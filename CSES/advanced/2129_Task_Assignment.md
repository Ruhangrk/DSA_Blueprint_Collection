# [2129 — Task Assignment](https://cses.fi/problemset/task/2129)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** Hungarian, min-cost matching, MCMF  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A company has `n` employees and there are `n` tasks that need to be done. We know for each employee the cost of carrying out each task. Every employee should be assigned to exactly one task. What is the minimum total cost if we assign the tasks optimally and how could they be assigned?

### Input

The first input line has one integer `n`: the number of employees and the number of tasks that need to be done.

After this, there are `n` lines each consisting of `n` integers. The `i`th line consists of integers `ci1, ci2, ..., cin`: the cost of each task when it is assigned to the `i`th employee.

### Output

First print the minimum total cost.

Then print `n` lines each consisting of two integers `a` and `b`: you assign the `b`th task to the `a`th employee.

If there are multiple solutions you can print any of them.

### Constraints

- `1 <= n <= 200`
- `1 <= cij <= 1000`

### Examples

**Input**
```
4
17 8 16 9
7 15 12 19
6 9 10 11
14 7 13 10
```

**Output**
```
33
1 4
2 1
3 3
4 2
```

Explanation: The minimum total cost is `33`. We can reach this by assigning employee `1` task `4`, employee `2` task `1`, employee `3` task `3` and employee `4` task `2`. This will cost `9+7+10+7=33`.

---

## What this problem really is

Minimum-cost perfect matching in a bipartite graph employees ↔ tasks (dense `n x n` cost matrix). Also recover the matching.

## What to use

| Tool | Why |
|------|-----|
| Hungarian (Kuhn–Munkres) | Classic dense assignment, `O(n^3)` fine for `n=200` |
| Min-cost max-flow | Source→employees→tasks→sink, unit caps, edge costs `cij` |
| Matching recovery | Read mate arrays / flow edges used |

## Approach

**Root idea:** each employee picks exactly one task; costs add. Optimal assignment is bipartite min-cost perfect matching.

**Mental picture:** a cost spreadsheet; choose `n` cells with no two in the same row or column, minimizing the sum — like a Latin placement of rooks on cheap squares.

**How that becomes the method (Hungarian sketch)**

1. Maintain row/column potentials and a partial matching.
2. For each free employee, grow a Hungarian tree / shortest augmenting path under reduced costs until a free task is found; update potentials; augment.
3. Sum `c[i][mate[i]]` for the minimum cost; print pairs `(i, mate[i])`.

Flow formulation: unit-capacity bipartite MCMF yields the same pairs.

**Data structures:** cost matrix `c[n][n]`; arrays `u[n+1]`, `v[n+1]`, `p[n+1]`, `way[n+1]` (1-indexed Hungarian variant); or residual graph for MCMF.

## Walkthrough (on a sample)

Matching `(1,4),(2,1),(3,3),(4,2)` costs `9+7+10+7=33`, which is optimal.

## Pitfalls

- Reporting only the cost without the assignment (required).  
- Using max-cost by mistake.  
- 0-based vs 1-based employee/task indices in output.

## Complexity

- Time: `O(n^3)` Hungarian  
- Space: `O(n^2)`

## Key takeaway

**Task assignment = min-cost bipartite perfect matching** (Hungarian or MCMF); print cost and pairs.
