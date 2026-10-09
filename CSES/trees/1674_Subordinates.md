# [1674 — Subordinates](https://cses.fi/problemset/task/1674)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** trees, DFS, subtree sizes  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given the structure of a company, your task is to calculate for each employee the number of their subordinates.

### Input

The first input line has an integer `n`: the number of employees. The employees are numbered `1, 2, ..., n`, and employee `1` is the general director of the company.

After this, there are `n-1` integers: for each employee `2, 3, ..., n` their direct boss in the company.

### Output

Print `n` integers: for each employee `1, 2, ..., n` the number of their subordinates.

### Constraints

- `1 <= n <= 2e5`

### Examples

**Input**
```
5
1 1 2 3
```

**Output**
```
4 1 1 0 0
```

---

## What this problem really is

Rooted tree (boss → employee edges). For each node, count nodes in its subtree **excluding itself** (proper subordinates).

## What to use

| Tool | Why |
|------|-----|
| Adjacency list children | Tree rooted at `1` |
| DFS / recursion or stack | Compute subtree sizes bottom-up |
| `sub[u] = sum(sub[v]+1 for children v)` | Each child contributes itself + its subordinates |

## Approach

**Root idea:** after processing all children of `u`, the subordinate count is the sum over children of `(1 + subordinates[child])`.

**Mental picture:** each manager’s headcount is everyone under them in the org chart, not counting themselves.

**Data structures**

- `vector<int> g[n+1]` — children lists.
- `int ans[n+1]`.

**Steps**

1. Read bosses: for employee `i = 2..n`, boss `b` → `g[b].push_back(i)`.
2. DFS from `1`: for each child, DFS child, then `ans[u] += ans[child] + 1`.
3. Print `ans[1..n]`.

## Walkthrough (on a sample)

Bosses: `2→1, 3→1, 4→2, 5→3`.

- `4,5` leaves → `0`
- `2` has child `4` → `1`; `3` has `5` → `1`
- `1` has `2` and `3` → `(1+1)+(1+1)=4`

## Pitfalls

- Counting the node itself in the answer.
- Recursion depth on a line tree — increase stack or use iterative DFS at `n=2e5`.
- Treating the input as undirected edges without rooting.

## Complexity

- Time: `O(n)`  
- Space: `O(n)`

## Key takeaway

**Subordinates = subtree size minus one** — DFS aggregates `ans[child]+1` over children.
