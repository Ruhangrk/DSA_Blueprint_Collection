# [1739 — Forest Queries II](https://cses.fi/problemset/task/1739)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** 2D Fenwick tree, point update, rectangle sum  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an `n x n` grid representing the map of a forest. Each square is either empty or has a tree. Your task is to process `q` queries of the following types:

1. Change the state (empty/tree) of a square.
2. How many trees are inside a rectangle in the forest?

### Input

The first input line has two integers `n` and `q`: the size of the forest and the number of queries.

Then, there are `n` lines describing the forest. Each line has `n` characters: `.` is an empty square and `*` is a tree.

Finally, there are `q` lines describing the queries. The format of each line is either `"1 y x"` or `"2 y1 x1 y2 x2"`.

### Output

Print the answer to each query of the second type.

### Constraints

- `1 <= n <= 1000`
- `1 <= q <= 2e5`
- `1 <= y, x <= n`
- `1 <= y1 <= y2 <= n`
- `1 <= x1 <= x2 <= n`

### Examples

**Input**
```
4 3
.*..
*.**
**..
****
2 2 2 3 4
1 3 3
2 2 2 3 4
```

**Output**
```
3
4
```

---

## What this problem really is

**2D point updates** (toggle tree/empty) plus **rectangle sum** queries. Static 2D prefix sums are not enough once the grid changes.

## What to use

| Tool | Why |
|------|-----|
| 2D Fenwick tree (BIT) | `O(log^2 n)` point add + prefix rectangle sum |
| Grid of `0/1` | Track current state to toggle with `+1` / `-1` |
| Inclusion-exclusion | Rectangle = four prefix corners |

`n <= 1000` so `O(n^2)` memory for the BIT is fine; `q` up to `2e5` needs polylog per op.

## Approach

**Root idea:** store each tree as `+1` in a 2D Fenwick. Toggle flips the cell by `±1`. Rectangle count is the classic four-prefix formula.

**Mental picture:** a spreadsheet of tree counts; you can flip one cell, and any axis-aligned block total still comes from four “from corner (1,1)” reads.

**Data structures**

- `bool g[y][x]` (or `int`) — current tree presence.
- 2D Fenwick `bit[y][x]` supporting:
  - `add(y, x, delta)` — add `delta` at cell `(y,x)`
  - `sumPrefix(y, x)` — sum of rectangle `(1..y) x (1..x)`

**Rectangle sum** on `[y1..y2] x [x1..x2]`:

```
sumPrefix(y2,x2) - sumPrefix(y1-1,x2) - sumPrefix(y2,x1-1) + sumPrefix(y1-1,x1-1)
```

**Steps**

1. Read grid; for every `*`, `add(y,x,+1)` and mark occupied.
2. Type `1 y x`: if tree, `add(y,x,-1)` and clear; else `add(y,x,+1)` and set.
3. Type `2`: print the four-corner Fenwick formula.

## Walkthrough (on a sample)

Grid rows: `.*..` / `*.**` / `**..` / `****`.

Rectangle `y=2..3`, `x=2..4`: row2 contributes `. * *` (2 trees), row3 contributes `* . .` (1 tree) → **3**.

Type `1` at `(3,3)`: was `.`, becomes `*`, Fenwick `add(3,3,+1)`. Same rectangle now **4**.

Each toggle is one `add` with `±1`; each answer is four `sumPrefix` calls.

## Pitfalls

- Mixing up `(y,x)` vs `(x,y)` — CSES uses **row y, column x**.
- Forgetting the `+ sumPrefix(y1-1,x1-1)` corner in inclusion-exclusion.
- Using static 2D prefix only — wrong after type-`1` updates.
- Toggling without checking current state (always `+1` would duplicate).

## Complexity

- Time: `O(n^2 log^2 n)` build + `O(q log^2 n)`  
- Space: `O(n^2)`

## Key takeaway

**Dynamic 2D rectangle sums = 2D Fenwick** with point `±1` toggles and four-prefix inclusion-exclusion.
