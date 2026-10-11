# [2415 — Functional Graph Distribution](https://cses.fi/problemset/task/2415)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** combinatorics, functional graphs, Stirling numbers  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A functional graph is a directed graph where each node has outdegree `1`.

Given `n`, your task is to calculate for each `k=1..n` the number of functional graphs that have `n` nodes and `k` components.

### Input

The only input line has an integer `n`: the number of nodes.

### Output

Print `n` lines: for each `k=1..n` the number of graphs modulo `1e9+7`.

### Constraints

- `1 <= n <= 5000`

### Examples

**Input**
```
3
```

**Output**
```
17
9
1
```

---

## What this problem really is

Count functions `f: {1..n} -> {1..n}` by the number of weakly connected components of the digraph with edges `i -> f(i)`.

## What to use

| Tool | Why |
|------|-----|
| Structure of functional graphs | Each component = one cycle with trees feeding in |
| Stirling 2nd kind `S(m,k)` | Partition a related set of “cycle-bearing” nodes into `k` nonempty unlabeled groups |
| Binomials + powers of `n` | Choose / map the remaining nodes |

## Approach

**Root idea:** total mappings are `n^n`. To track components, use a known closed form that mixes powers of `n` with Stirling numbers of the second kind.

**Mental picture:** each component has exactly one cycle. Choosing which nodes “support” the component decomposition can be encoded by: pick a set related to cycle structure, partition it into `k` nonempty unlabeled blocks (Stirling), and map everyone else arbitrarily into the `n` labels with the usual functional-graph counting identity.

**How that becomes the method**

A standard formula used for this CSES task:

For each `k = 1..n`:

`ans[k] = sum_{j=0..n-1} C(n-1, j) * n^{n-1-j} * S(j+1, k)`

where `S(a,b)` is a Stirling number of the second kind (`S(a,b)=0` if `b>a` or `b=0<a`).

Compute:

1. Stirling table: `S[0][0]=1`, `S[i][j] = S[i-1][j-1] + (i-1)*S[i-1][j]`.
2. Binomials `C[0..n][0..n]` (or fact/invfact).
3. Powers `powN[t] = n^t mod MOD` for `t=0..n`.
4. For each `k`, evaluate the sum; print on its own line.

Check: sum of `ans[k]` equals `n^n` (`27` for `n=3`: `17+9+1=27`).

**Data structures:** `S[0..n][0..n]`, `C` or facts, `powN[0..n]`.

## Walkthrough (on a sample)

`n=3`:

| `k` | sum result |
|-----|------------|
| 1 | `17` |
| 2 | `9` |
| 3 | `1` |

The single `k=3` graph: every node is its own loop-component — only the identity mapping `f(i)=i` has three components among the `27` functions? Actually three components means three separate cycles/trees; for `n=3` the only 3-component functional graph is three self-loops. That matches `ans[3]=1`.

## Pitfalls

- Confusing *weak* components (the problem’s “components” in functional-graph language) with number of cycles alone.
- Stirling indexing: need `S[j+1][k]`, not `S[j][k]`.
- `n=5000` → `O(n^2)` tables are mandatory; do not recompute Stirling per query `k` from scratch beyond one table.
- Output is `n` lines (one number per line), unlike some distribution problems that print one line.

## Complexity

- Time: `O(n^2)`
- Space: `O(n^2)` for Stirling (can optimize Stirling row-by-row if needed)

## Key takeaway

Functional graphs counted by component number collapse to a Stirling-powered sum: `sum_j C(n-1,j) n^{n-1-j} S(j+1,k)`.
