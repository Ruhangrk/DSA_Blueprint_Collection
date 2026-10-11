# [2134D — Sliding Tree](https://codeforces.com/problemset/problem/2134/D)

**Platform:** Codeforces
**Source:** Codeforces Round 1045 (Div. 2)
**Tags:** constructive algorithms, dfs and similar, greedy, implementation, trees
**Difficulty / Rating:** 2300
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Codeforces Round 1045 (Div. 2) Editorial](https://codeforces.com/blog/entry/145832)

---

## Problem Statement

You are given a tree with `n` vertices numbered `1` to `n`. You may apply this operation:

- Choose three distinct vertices `a`, `b`, `c` such that `b` is directly connected to both `a` and `c`.
- For every neighbor `d` of `b` (excluding `a` and `c`), remove the edge `b-d` and connect `d` directly to `c`.

The result is still a tree. Transform the tree into a path graph (every vertex has degree at most 2) using the minimum number of operations. Output only the first operation of an optimal sequence, or `-1` if the tree is already a path.

### Input

The first line contains `t` (`1 <= t <= 1e4`). Each test case starts with `n` (`1 <= n <= 2e5`), followed by `n - 1` edges `u v`. The sum of `n` over all test cases does not exceed `2e5`.

### Output

For each test case, print `-1` if no operation is needed. Otherwise print three integers `a b c`, the first operation of an optimal sequence. Any valid choice is accepted.

### Constraints

- `1 <= t <= 1e4`
- `1 <= n <= 2e5`
- Sum of `n` `<= 2e5`
- The input is a tree

### Examples

Input:

```
4
6
4 3
3 5
3 1
1 2
3 6
1
2
1 2
5
5 4
2 3
4 2
1 4
```

Output:

```
4 3 5
-1
-1
2 4 1
```

### Note

In the first test, the minimum is 2 operations, and the first operation shown is `a = 4`, `b = 3`, `c = 5`. Trees of one vertex or a single edge are already paths. Any valid first operation works when several exist.

---

## What this problem really is

Each sliding operation can increase the diameter of the tree by at most 1, since the path between any two vertices can gain at most one edge. A path graph has diameter `n - 1`. So the answer is a lower bound on the number of operations, and the real question is whether every operation can be made to add exactly 1 to the diameter.

## What to use

| Tool | Why |
|------|-----|
| Diameter of the tree (`l` edges) | Lower bound: at least `(n - 1) - l` operations, since each operation adds at most 1. |
| Two BFS/DFS passes | Find a diameter endpoint and the diameter path. |
| Rooting the tree at a diameter endpoint | Gives every diameter vertex a well-defined parent toward the root. |
| Greedy choice of one edge leaving the diameter | Each operation moves a non-diameter subtree onto the diameter, adding exactly 1. |

## Approach

**Root idea:** The answer is `-1` if the tree is already a path (diameter `n - 1`). Otherwise, the first operation is any edge that leaves the diameter, with the operation pivot placed at the diameter.

**Why the bound is tight:** Consider a path `p` between two vertices. An operation `(a, b, c)` only changes path lengths at `b`, and only when the path enters `b` through `a` or `c` and leaves through a different neighbor. In that case the length increases by at most 1. So the diameter grows by at most 1 per operation, giving the lower bound `(n - 1) - l`.

**Why the greedy always achieves it:** Take a diameter and root the tree at one of its endpoints. Find a diameter vertex `u` that has a neighbor `v` not on the diameter. Use:

- `b = u`
- `c = v`
- `a = parent of u` (in the rooted tree, which lies on the diameter because `u` is not the root)

This operation moves the subtrees hanging off `u` (other than `a` and `c`) onto `v`, extending the diameter by exactly one step. Repeating keeps the invariant, so the minimum is `(n - 1) - l`. Only the first operation is output, and this choice is what the output requires.

**Mental picture:** Think of the diameter as a spine. Each operation picks one side branch and pulls it into a new leaf on the spine, lengthening the spine by one.

**How that becomes the method:**
1. Run a DFS/BFS from vertex `1` to find the farthest vertex `x`.
2. Run a DFS/BFS from `x` to find the farthest vertex `y` and record parents from `x`. The distance `dist(x, y)` is the diameter `l`.
3. If `l == n - 1`, output `-1`.
4. Otherwise, mark the diameter path from `y` to `x` using parents.
5. Scan vertices `u` on the diameter. For each neighbor `v` not on the diameter, output `parent(u) b=u c=v`, with `a = parent(u)` as the diameter neighbor toward `x`.

**Data structures:**
- Adjacency lists.
- Parent array, distance array, and an "on diameter" boolean array.

## Walkthrough (on a sample)

Test 4: `n = 5`, edges `5-4`, `2-3`, `4-2`, `1-4`.

Vertex 4 has neighbors 5, 2, 1. Vertex 2 has neighbors 3, 4.

Diameter: `3 - 2 - 4 - 5` with length `l = 3`. Lower bound `(n - 1) - l = 4 - 3 = 1` operation.

Root at endpoint `3`. Path: `3 -> 2 -> 4 -> 5`. Vertex `4` is on the diameter and has neighbor `1` off the diameter. Set `u = 4`, `v = 1`. Parent of `4` toward `3` is `2`, so `a = 2`, `b = 4`, `c = 1`.

Check: `b = 4` has neighbors `2` (= a), `5` and `1` (= c). Excluding `a` and `c`, only neighbor `5` remains. Remove `4-5` and connect `5` to `1`. The edges become `2-3`, `4-2`, `4-1`, `1-5`, which is the path `3 - 2 - 4 - 1 - 5`. Output `2 4 1`, matching the sample.

## Pitfalls

- **Picking the wrong pivot:** `a` must be the diameter neighbor of `b` toward the root, not any neighbor. Using a non-diameter neighbor lengthens nothing.
- **Forgetting the `-1` case:** A tree whose diameter equals `n - 1` is already a path. A single vertex is also a path.
- **Choosing a non-diameter `u`:** `b` must lie on the diameter. Otherwise the diameter does not grow.
- **Rooting at a non-endpoint:** Parent pointers toward the root must follow the diameter. Root at a diameter endpoint.
- **Multiple valid answers:** Any valid first operation is accepted, so the output need not match the sample exactly.

## Complexity

- Time: `O(n)` per test case (three linear traversals).
- Space: `O(n)`.

## Key takeaway

Each operation raises the diameter by at most 1, and rooting at a diameter endpoint and pulling one off-diameter neighbor onto the spine always raises it by exactly 1, so the answer is `(n - 1) - diameter`.
