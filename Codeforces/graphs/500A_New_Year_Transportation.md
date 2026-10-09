# [500A — New Year Transportation](https://codeforces.com/problemset/problem/500/A)

**Platform:** Codeforces  
**Source:** Good Bye 2014  
**Tags:** dfs and similar, graphs, implementation  
**Difficulty / Rating:** *1000*  
**Time limit:** 2 seconds  
**Memory limit:** 256 megabytes  
**Editorial:** [Good Bye 2014 Editorial](https://codeforces.com/blog/entry/15488) · [Tutorial](https://codeforces.com/blog/entry/15513)

---

## Problem Statement

New Year is coming in Line World! In this world, there are `n` cells numbered by integers from `1` to `n`, as a `1 x n` board. People live in cells. However, it was hard to move between distinct cells, because of the difficulty of escaping the cell. People wanted to meet people who live in other cells.

So, user tncks0121 has made a transportation system to move between these cells, to celebrate the New Year. First, he thought of `n-1` positive integers `a1, a2, ..., a(n-1)`. For every integer `i` where `1 <= i <= n-1` the condition `1 <= ai <= n-i` holds. Next, he made `n-1` portals, numbered by integers from `1` to `n-1`. The `i`-th (`1 <= i <= n-1`) portal connects cell `i` and cell `(i + ai)`, and one can travel from cell `i` to cell `(i + ai)` using the `i`-th portal. Unfortunately, one cannot use the portal backwards, which means one cannot move from cell `(i + ai)` to cell `i` using the `i`-th portal. It is easy to see that because of condition `1 <= ai <= n-i` one can't leave the Line World using portals.

Currently, I am standing at cell `1`, and I want to go to cell `t`. However, I don't know whether it is possible to go there. Please determine whether I can go to cell `t` by only using the constructed transportation system.

### Input

The first line contains two space-separated integers `n` (`3 <= n <= 3e4`) and `t` (`2 <= t <= n`) — the number of cells, and the index of the cell which I want to go to.

The second line contains `n-1` space-separated integers `a1, a2, ..., a(n-1)` (`1 <= ai <= n-i`). It is guaranteed, that using the given transportation system, one cannot leave the Line World.

### Output

If I can go to cell `t` using the transportation system, print `"YES"`. Otherwise, print `"NO"`.

### Constraints

- `3 <= n <= 3e4`
- `2 <= t <= n`
- `1 <= ai <= n-i`

### Examples

**Input**
```
8 4
1 2 1 2 1 2 1
```

**Output**
```
YES
```

**Input**
```
8 5
1 2 1 2 1 1 1
```

**Output**
```
NO
```

### Note

In the first sample, the visited cells are: `1, 2, 4`; so we can successfully visit the cell `4`.

In the second sample, the possible cells to visit are: `1, 2, 4, 6, 7, 8`; so we can't visit the cell `5`, which we want to visit.

---

## What this problem really is

Cells are nodes. Each portal is a **directed edge** `i -> i + a[i]`. You start at `1` and ask: **is `t` reachable?**

## What to use

| Tool | Why |
|------|-----|
| Graph modeling | Portals = directed edges |
| Reachability (DFS / BFS / walk) | Check if `t` lies on the unique path from `1` |
| Simple forward loop | Each node has **out-degree <= 1**, so the path from `1` is unique |

## Approach

1. Model cell `i` jumping to `i + a[i]`.
2. Start at position `1`.
3. Keep jumping forward while the current cell is still **before** `t`.
4. If you land exactly on `t`, answer `YES`; if you skip past it (or never hit it), answer `NO`.

Jumps only increase the index, so you never need to go backwards.

## Walkthrough (on a sample)

Sample 1: `a = [1, 2, 1, 2, 1, 2, 1]`, `t = 4`

```
1 → 2 → 4 → 6 → 7 → 8
         ↑
         land on t → YES
```

Sample 2: same idea, path is `1 → 2 → 4 → 6 → 7 → 8`. Cell `5` is never visited → `NO`.

## Pitfalls

- **`t = n`**: pretests often skipped this; you must still check whether the walk reaches the last cell (famous test 13 / hack case).
- **Overshooting**: landing past `t` is `NO` — portals are one-way; you cannot step back.
- **Thinking you need a full graph library**: out-degree `<= 1` means a single walk is enough; DFS/BFS still work if you want the general pattern.

## Complexity

- Time: `O(n)` — each cell visited at most once  
- Space: `O(n)` for storing `a` (or `O(1)` extra beyond input)

## Key takeaway

Strip the story → **directed reachability**. When every node has one outgoing edge, follow the unique path from the start and see if the target appears.
