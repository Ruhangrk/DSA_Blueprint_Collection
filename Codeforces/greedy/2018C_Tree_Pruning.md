# [2018C — Tree Pruning](https://codeforces.com/problemset/problem/2018/C)

**Platform:** Codeforces
**Source:** Codeforces Round 975 (Div. 1)
**Tags:** brute force, dfs and similar, greedy, sortings, trees
**Difficulty / Rating:** 1700
**Time limit:** 3 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Round 975 editorial (Problem 2018C section)](https://codeforces.com/blog/entry/134420)

---

## Problem Statement

You are given a tree with `n` nodes, rooted at node `1`. In this problem, a leaf is a non-root node with degree `1`.

In one operation, you can remove a leaf and the edge adjacent to it (possibly, new leaves appear). What is the minimum number of operations that you have to perform to get a tree, also rooted at node `1`, where all the leaves are at the same distance from the root?

### Input

Each test contains multiple test cases. The first line contains the number of test cases `t` (`1 <= t <= 1e4`). The description of the test cases follows.

The first line of each test case contains a single integer `n` (`3 <= n <= 5e5`) — the number of nodes.

Each of the next `n - 1` lines contains two integers `u`, `v` (`1 <= u, v <= n`, `u != v`), describing an edge that connects `u` and `v`. It is guaranteed that the given edges form a tree.

It is guaranteed that the sum of `n` over all test cases does not exceed `5e5`.

### Output

For each test case, output a single integer: the minimum number of operations needed to achieve your goal.

### Constraints

- `1 <= t <= 1e4`
- `3 <= n <= 5e5`
- The edges form a tree.
- Sum of `n` over all test cases `<= 5e5`

### Examples

Input:

```
3
7
1 2
1 3
2 4
2 5
4 6
4 7
7
1 2
1 3
1 4
2 5
3 6
5 7
15
12 9
1 6
6 14
9 11
8 7
3 5
13 5
6 10
13 15
13 6
14 12
7 2
8 1
1 4
```

Output:

```
2
2
5
```

### Note

In the first two examples, the tree is as follows:

In the first example, by removing edges `(1, 3)` and `(2, 5)`, the resulting tree has all leaves (nodes `6` and `7`) at the same distance from the root (node `1`), which is `3`. The answer is `2`, as it is the minimum number of edges that need to be removed to achieve the goal.

In the second example, removing edges `(1, 4)` and `(5, 7)` results in a tree where all leaves (nodes `4` and `5`) are at the same distance from the root (node `1`), which is `2`.

---

## What this problem really is

Delete as few nodes as possible (always deleting current leaves) so that every remaining leaf sits at one common depth from the root.

## What to use

| Tool | Why |
|------|-----|
| DFS / BFS from root `1` | Gets each node's depth `dep[v]` and the deepest depth `mx[v]` in its subtree. |
| Depth intervals | A node stays when the chosen final leaf depth `d` lies in `[dep[v], mx[v]]`. |
| Difference array / sweep | Counts how many nodes cover each possible `d`, then picks the best `d`. |

## Approach

**Root idea (from the editorial):** Fix the final leaf depth `d`. The optimal kept tree for that `d` is exactly the set of nodes that still have a path down to depth `d`: every node at depth `d`, plus all of their ancestors. Minimizing deletions is the same as maximizing the number of kept nodes over the choice of `d`.

**Mental picture:** Root the tree at `1`. For a target height `d`, keep the "trunks" that can still reach a leaf at depth `d`. A node `v` belongs on such a trunk if and only if:
- its own depth satisfies `dep[v] <= d` (it is not deeper than the target), and
- some node in its subtree has depth at least `d` (it can still reach the target).

So node `v` is kept for every target `d` in the closed interval `[dep[v], mx[v]]`.

**How that becomes the method:**
1. Root the tree at `1`. For every node `v`, compute `dep[v]` and `mx[v]` = maximum depth in the subtree of `v`.
2. For every node `v`, mark that it covers depths from `dep[v]` to `mx[v]` (difference array: `+1` at `dep[v]`, `-1` at `mx[v] + 1`).
3. Sweep depths from `0` to the global max depth and find `best` = the maximum number of covering nodes at any depth `d`.
4. Answer = `n - best` (nodes removed = operations).

**Data structures:**
- Adjacency list for the tree.
- Arrays `dep[]` and `mx[]`.
- A difference array over depths `0..n` (depth is at most `n - 1`).

## Walkthrough (on a sample)

First test: root `1`, edges as in the sample. Depths:

- `dep[1] = 0`, `dep[2] = 1`, `dep[3] = 1`, `dep[4] = 2`, `dep[5] = 2`, `dep[6] = 3`, `dep[7] = 3`.
- Subtree max depths: `mx[6] = mx[7] = 3`, `mx[4] = 3`, `mx[5] = 2`, `mx[2] = 3`, `mx[3] = 1`, `mx[1] = 3`.

Intervals `[dep, mx]`:
- `1: [0, 3]`, `2: [1, 3]`, `3: [1, 1]`, `4: [2, 3]`, `5: [2, 2]`, `6: [3, 3]`, `7: [3, 3]`.

Coverage by depth:
- `d = 0`: only `1` → `1` node.
- `d = 1`: `1, 2, 3` → `3` nodes.
- `d = 2`: `1, 2, 4, 5` → `4` nodes.
- `d = 3`: `1, 2, 4, 6, 7` → `5` nodes.

Best keep = `5`, so answer = `7 - 5 = 2`, matching the sample (keep the path down to leaves at depth `3`).

## Pitfalls

- **Treating the root as a leaf:** By definition the root is never a leaf, even if it ends with degree `1`.
- **Keeping only depth-`d` nodes:** Ancestors must stay, or the tree disconnects from the root.
- **Answer as `best` instead of `n - best`:** The problem asks for deletions, not kept nodes.
- **Deep recursion:** `n` can be `5e5`; use iterative DFS/BFS or raise the stack carefully.

## Complexity

- Time: `O(n)` per test for the DFS and the depth sweep. Over all tests, `O(sum of n)`.
- Space: `O(n)`.

## Key takeaway

Fix the final leaf depth `d`, keep exactly the nodes whose depth interval `[dep, subtreeMax]` covers `d`, and choose the `d` that keeps the most nodes.
