# [2071C — Trapmigiano Reggiano](https://codeforces.com/problemset/problem/2071/C)

**Platform:** Codeforces
**Source:** Codeforces Round 1007 (Div. 2)
**Tags:** constructive algorithms, data structures, dfs and similar, dp, greedy, sortings, trees
**Difficulty / Rating:** 1700
**Time limit:** 2 seconds
**Memory limit:** 256 megabytes
**Editorial:** [Round 1007 editorial (Problem C section)](https://codeforces.com/blog/entry/140115)

---

## Problem Statement

In an Italian village, a hungry mouse starts at vertex `st` of a given tree with `n` vertices.

Given a permutation `p` of length `n`, there are `n` steps. For the `i`-th step:

- A tempting piece of Parmesan cheese appears at vertex `p[i]`.
- If the mouse is currently at vertex `p[i]`, it stays there.
- Otherwise, it moves along the simple path to vertex `p[i]` by one edge.

Find a permutation such that, after all `n` steps, the mouse is at vertex `en`, where a trap awaits. The mouse must be at `en` after all `n` steps, though it may pass through `en` earlier.

A tree is a connected graph without cycles. A permutation of length `n` is an array of `n` distinct integers from `1` to `n` in any order.

### Input

The first line contains `t` (`1 <= t <= 1e4`), the number of test cases. Each test case has:
- a line with `n`, `st`, and `en` (`1 <= n <= 1e5`, `1 <= st, en <= n`);
- `n - 1` lines, each with two integers `u` and `v` (`1 <= u, v <= n`, `u != v`), an edge of the tree.

The sum of `n` over all test cases does not exceed `1e5`.

### Output

For each test case:
- If no such permutation exists, print `-1`.
- Otherwise, print `n` integers `p[1], p[2], ..., p[n]`, the order in which the cheese appears, so that the mouse is caught at `en`.

If there are multiple solutions, print any of them.

### Constraints

- `1 <= t <= 1e4`
- `1 <= n <= 1e5`
- `1 <= st, en <= n`
- The edges form a tree.
- Sum of `n` over all test cases `<= 1e5`

### Examples

Input:

```
4
1 1 1
2 1 2
1 2
3 2 2
1 2
2 3
6 1 4
1 2
1 3
4 5
5 6
1 4
```

Output (one valid answer per test; any valid answer is accepted):

```
1
1 2
3 1 2
1 4 3 2 6 5
```

### Note

- Test 1: `n = 1`, the only permutation is `[1]`. The mouse starts at `1` = `en`.
- Test 2: `p = [1, 2]`. The cheese at `1` leaves the mouse at `1`, then the cheese at `2` moves it to `2 = en`.
- Test 3: `p = [3, 1, 2]`. The mouse goes `2 -> 3`, then `3 -> 2`, then stays at `2 = en`.

---

## What this problem really is

Each cheese pulls the mouse one step toward it, unless the mouse is already there. We need to choose an order of all `n` cheeses so that the mouse finishes at a specific vertex `en`.

## What to use

| Tool | Why |
|------|-----|
| Rooting the tree at `en` | Turns "distance to the trap" into depth, so every move is either up or down the tree. |
| BFS or DFS | Computes each vertex's depth from `en` in `O(n)`. An iterative BFS avoids deep recursion. |
| Sorting by depth, deepest first | Guarantees that the mouse never goes deeper than the level currently being handled. |

## Approach

**Root idea:** Root the tree at `en`. Place the cheeses from the deepest level up to the root. The mouse can never end up deeper than the level that is currently being handled, so once the root's cheese is placed, the mouse is at `en`.

**Mental picture:** Picture the tree hanging from `en` as the top anchor. The mouse is a climber on the branches. A cheese placed in one of the mouse's own branches below it pulls the mouse down. Any other cheese pulls the mouse up. Handling the deepest cheeses first means the mouse can climb only as high as the next level, and it can never sink below that level, because the cheeses left to handle are all shallower. By the time we reach depth 0, the mouse has been pulled up to `en`.

**Why depth never exceeds the current level:** Suppose all cheeses at depth greater than `d` have been handled. Then the mouse is at depth `<= d+1` going into the depth-`d` group, and the claim is that after each cheese in the group the mouse is at depth `<= d`:
- If the mouse is at depth `d+1`, the cheese at depth `d` is not below it, so the mouse climbs to depth `d`.
- If the mouse is at depth `<= d`, its next step can only go down to a vertex that is deeper than itself and on the path to the cheese. Since the cheese is at depth `d`, the step lands at depth `<= d`.

So after processing depth `d`, the mouse is at depth `<= d`. Applied down to depth `0`, the mouse ends at `en`.

**How that becomes the method:**
1. Root the tree at `en` and compute each vertex's depth with a BFS.
2. Sort all vertices by depth, descending. Ties may go in any order.
3. Output that order. The vertex `en` (depth `0`) comes last automatically.
4. The answer is never `-1`.

**Data structures:**
- An adjacency list for the tree.
- A depth array computed from `en`.
- A list of vertices bucketed by depth, which gives the descending order directly.

## Walkthrough (on a sample)

Test 4: `n = 6`, `st = 1`, `en = 4`. Edges: `1-2`, `1-3`, `4-5`, `5-6`, `1-4`.

- Root at `4`. Depths: `4 -> 0`, `1 -> 1`, `5 -> 1`, `2 -> 2`, `3 -> 2`, `6 -> 2`.
- Order by depth descending: depth 2 first (`2, 3, 6`), then depth 1 (`1, 5`), then depth 0 (`4`). Output: `2 3 6 1 5 4`.
- Trace, mouse starts at `1`:
  - Cheese `2`: mouse at `1` moves toward `2` to `2`. Depth is now `2`, which is within the depth-2 group's limit.
  - Cheese `3`: mouse at `2` moves toward `3` by way of `1` to `1`. Depth `1`.
  - Cheese `6`: mouse at `1` moves toward `6` along `1-4-5-6`, one step to `4`. Depth `0`.
  - Cheese `1`: mouse at `4` moves toward `1` to `1`. Depth `1`.
  - Cheese `5`: mouse at `1` moves toward `5` along `1-4-5` to `4`. Depth `0`.
  - Cheese `4`: mouse is already at `4`, so it stays.
- Final position: `4 = en`. The output is valid.

## Pitfalls

- **Ascending depth order:** Handling shallow cheeses first can let the mouse sink deep, and it may never come back. Always go deepest first.
- **Rooting at `st`:** The depths must be measured from `en`, the trap, not from the starting vertex.
- **`-1` is never needed:** Every tree has a valid order, so the `-1` case never occurs. Still print the order for every test.
- **`st = en`:** The same order works. The mouse starts at the trap and can only be pulled away when a cheese is placed in its subtree, which the depth order handles.
- **Recursion depth:** A path of length `1e5` can overflow a recursive DFS. Use BFS.

## Complexity

- Time: `O(n)` per test for the BFS and the bucket sort by depth.
- Space: `O(n)`.

## Key takeaway

Root the tree at the trap and place the cheeses from deepest to shallowest. The mouse can never go deeper than the level currently being handled, so after the root's cheese it must be at the trap.
