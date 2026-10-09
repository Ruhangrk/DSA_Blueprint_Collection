# [1652 — Forest Queries](https://cses.fi/problemset/task/1652)

**Platform:** CSES  
**Source:** CSES Problem Set — Range Queries  
**Tags:** 2D prefix sums  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an `n x n` grid representing the map of a forest. Each square is either empty or contains a tree. The upper-left square has coordinates `(1,1)`, and the lower-right square has coordinates `(n,n)`.

Your task is to process `q` queries of the form: how many trees are inside a given rectangle in the forest?

### Input

The first input line has two integers `n` and `q`: the size of the forest and the number of queries.

Then, there are `n` lines describing the forest. Each line has `n` characters: `.` is an empty square and `*` is a tree.

Finally, there are `q` lines describing the queries. Each line has four integers `y1, x1, y2, x2` corresponding to the corners of a rectangle.

### Output

Print the number of trees inside each rectangle.

### Constraints

- `1 <= n <= 1000`
- `1 <= q <= 2e5`
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
2 2 3 4
3 1 3 1
1 1 2 2
```

**Output**
```
3
1
2
```

---

## What this problem really is

Static 2D grid of 0/1 (tree or not); many axis-aligned rectangle sum queries.

## What to use

| Tool | Why |
|------|-----|
| 2D prefix `sum[y][x]` | Trees in rectangle via inclusion-exclusion |
| `O(1)` per query | `q` is large; `n` only 1000 for build |

## Approach

**Root idea:** `sum[y][x]` = trees in the rectangle from `(1,1)` to `(y,x)`. Any sub-rectangle is four corners of that table.

**Mental picture:** running totals down-right; cancel the parts above and left of your window.

**Formula** for trees in `[y1,x1]–[y2,x2]`:

```
sum[y2][x2] - sum[y1-1][x2] - sum[y2][x1-1] + sum[y1-1][x1-1]
```

**Build**

```
sum[y][x] = (grid[y][x]=='*') + sum[y-1][x] + sum[y][x-1] - sum[y-1][x-1]
```

**Steps**

1. Read grid; build `sum`.
2. Answer each query with the 4-term formula.

## Walkthrough (on a sample)

Rectangle `(2,2)–(3,4)` covers three `*` → **`3`**; single cell `(3,1)` → **`1`**; `(1,1)–(2,2)` → **`2`**.

## Pitfalls

- Mixing `(y,x)` order vs `(row,col)`.
- Forgetting the `+ sum[y1-1][x1-1]` inclusion term.
- Scanning the rectangle per query — TLE on `q`.

## Complexity

- Time: `O(n^2 + q)`  
- Space: `O(n^2)`

## Key takeaway

**2D range sum = 2D prefix sums.** Rectangle count is one inclusion-exclusion of four prefixes.
