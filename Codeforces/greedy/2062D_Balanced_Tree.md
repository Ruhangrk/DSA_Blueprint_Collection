# [2062D — Balanced Tree](https://codeforces.com/problemset/problem/2062/D)

**Platform:** Codeforces
**Source:** Ethflow Round 1 (Codeforces Round 1001, Div. 1 + Div. 2)
**Tags:** dfs and similar, dp, graphs, greedy, trees
**Difficulty / Rating:** 2200
**Time limit:** 3 seconds
**Memory limit:** 512 megabytes
**Editorial:** [Ethflow Round 1 (Codeforces Round 1001) Editorial (Problem D section)](https://codeforces.com/blog/entry/138866)

---

## Problem Statement

You are given a tree with `n` nodes. Node `i` has a range `[l[i], r[i]]`. You choose an initial value `a[i]` with `l[i] <= a[i] <= r[i]`. A tree is balanced if all node values are equal, and its value is that common value.

In one operation, choose two nodes `u` and `v` (possibly equal), and increase the values of all nodes in the subtree of `v` by `1`, when the tree is rooted at `u`.

Perform operations so that the tree becomes balanced. Find the minimum possible final value. You do not need to minimise the number of operations.

### Input

The first line contains `t` (`1 <= t <= 1e5`).

For each test case, the first line contains `n` (`1 <= n <= 2e5`). Then `n` lines follow with `l[i]` and `r[i]` (`0 <= l[i] <= r[i] <= 1e9`). Then `n - 1` edges `u[i], v[i]`.

The sum of `n` over all test cases does not exceed `2e5`.

### Output

For each test case, output the minimum possible common value. It can be shown that the answer always exists.

### Constraints

- `1 <= t <= 1e5`
- `1 <= n <= 2e5`
- `0 <= l[i] <= r[i] <= 1e9`
- Sum of `n` `<= 2e5`

### Examples

Input:

```
6
4
0 11
6 6
0 0
5 5
2 1
3 1
4 3
7
1 1
0 5
0 5
2 2
2 2
2 2
2 2
1 2
1 3
2 4
2 5
3 6
3 7
4
1 1
1 1
1 1
0 0
1 4
2 4
3 4
7
0 20
0 20
0 20
0 20
3 3
4 4
5 5
1 2
1 3
1 4
2 5
3 6
4 7
5
1000000000 1000000000
0 0
1000000000 1000000000
0 0
1000000000 1000000000
3 2
2 1
1 4
4 5
6
21 88
57 81
98 99
61 76
15 50
23 67
2 1
3 2
4 3
5 3
6 4
```

Output:

```
11
3
3
5
3000000000
98
```

### Note

In the first test case, choose `a = [6, 6, 0, 5]`. Operation `u = 4, v = 3` runs 5 times, then `u = 1, v = 3` runs 6 times. The result is `11`, and no choice does better.

---

## What this problem really is

Each operation adds `1` to a whole subtree, and the root can be picked freely, so an operation effectively adds `1` to one side of an edge and not the other. The task is to pick starting values inside their ranges so that the cost of equalising the tree is as small as possible, and then the final common value is the cost plus the starting value of one node.

## What to use

| Tool | Why |
|------|-----|
| Root the tree at node `1` | Gives each edge a clear parent and child side, so the cost of each edge is local. |
| Edge cost as a difference | Making `a[child] = a[parent]` on an edge takes exactly `|a[child] - a[parent]|` operations that touch that edge. |
| Bottom-up greedy for starting values | Each node's value is chosen from its children's values, so the difference costs are minimised. |
| Sum over edges of positive child-minus-parent differences | Shows the total extra value added to the root's component, giving the final formula. |

## Approach

**Root idea:** Assume the starting values are fixed and the root is node `1`. For each edge between a child `x` and its parent `p`, make `a[x] = a[p]` by doing exactly `|a[x] - a[p]|` operations on that edge. The operations that raise the child side also raise the root's component. So the final value is:

`a[1] + sum over non-root nodes x of max(0, a[x] - a[parent(x)])`

Now choose the starting values to minimise this. Process nodes bottom-up. For a node `u`, pick:

`a[u] = min(r[u], max(l[u], max of a[child] over children))`

**Why this choice:** If the upper bound `r[u]` is infinite, a node should be at least as large as every child. Lowering it below a child's value only increases that child's positive difference, and can reduce the node's own difference by at most one for each unit lowered, so it never helps. The clamp to `[l[u], r[u]]` keeps the value legal. If a child's value is larger than `r[u]`, the extra cost is paid through the positive-difference term.

**Mental picture:** Think of each node as a water level. A child higher than its parent is an uphill pour that must be paid for. So each parent tries to rise to meet its tallest child, as far as its own range allows. Whatever height remains above a parent is counted as a cost on that edge.

**How that becomes the method:**
1. Root the tree at `1` and get a parent for every node, with a traversal order where children come before parents.
2. For each node `u` in that order, set `a[u] = min(r[u], max(l[u], max over children of a[child]))`.
3. Answer `= a[1] + sum over every child x of max(0, a[x] - a[parent(x)])`.

**Data structures:**
- Adjacency lists for the tree.
- A parent array and a post-order (children first) list of nodes.
- An array `a` of chosen values, and a 64-bit running sum of edge costs.

## Walkthrough (on a sample)

Test 1: `n = 4`. Ranges: node 1 `[0, 11]`, node 2 `[6, 6]`, node 3 `[0, 0]`, node 4 `[5, 5]`. Edges: `2-1`, `3-1`, `4-3`. Rooted at `1`: children of `1` are `2` and `3`; child of `3` is `4`.

Bottom-up:

- Node 2 is a leaf. `a[2] = min(6, max(6, nothing)) = 6`.
- Node 4 is a leaf. `a[4] = min(5, max(5, nothing)) = 5`.
- Node 3 has child 4 with value `5`. `max(l[3] = 0, 5) = 5`, then `min(r[3] = 0, 5) = 0`. So `a[3] = 0`.
- Node 1 has children with values `6` (node 2) and `0` (node 3). `max(l[1] = 0, 6, 0) = 6`, `min(11, 6) = 6`. So `a[1] = 6`.

Edge costs:

- Node 2 versus parent 1: `max(0, 6 - 6) = 0`.
- Node 3 versus parent 1: `max(0, 0 - 6) = 0`.
- Node 4 versus parent 3: `max(0, 5 - 0) = 5`.

Answer `6 + 5 = 11`. This matches the sample, and the chosen values are `[6, 6, 0, 5]`, the same as the note.

## Pitfalls

- **Rooting inconsistently:** The formula uses the child-minus-parent direction. Pick one root (node `1`) and stick with it.
- **Using `a[u] = min(r[u], ...)` without the lower bound:** The `max` with `l[u]` must come before the `min` with `r[u]`, so the value stays in range.
- **Summing negative differences:** Only positive child-minus-parent differences add cost; negative ones are free.
- **Forgetting the root's own value:** The answer is `a[1]` plus the edge costs, not just the edge costs.
- **Overflow:** Values reach `1e9` and sums of edge costs can reach `2e5 * 1e9`. Use 64-bit integers.
- **Deep recursion:** A path of `2e5` nodes can overflow the call stack. Use an explicit stack or BFS order.

## Complexity

- Time: `O(n)` per test for the traversal and the single bottom-up pass.
- Space: `O(n)`.

## Key takeaway

On a tree where each edge's cost is a difference of endpoint values, choose each node's value greedily from its children, clamped to its own range, and pay only for upward differences.
