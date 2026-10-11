# [3360 — Corner Subgrid Check](https://cses.fi/problemset/task/3360)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** C4 detection, bipartite, grid  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a grid of letters. Your task is to find subgrids whose height and width is at least two and all the corners have the same letter.

For each letter, check if there is a valid subgrid whose corners have that letter.

### Input

The first line has two integers `n` and `k`: the size of the grid and the number of letters. The letters are the first `k` uppercase letters.

After this, there are `n` lines that describe the grid. Each line has `n` letters.

### Output

Print `k` lines: for each letter, `YES` if there is a valid subgrid and `NO` otherwise.

### Constraints

- `1 <= n <= 3000`
- `1 <= k <= 26`

### Examples

**Input**
```
4 5
AAAA
CBBC
CBBE
AAAA
```

**Output**
```
YES
YES
NO
NO
NO
```

---

## What this problem really is

Per letter `L`: does there exist a pair of distinct rows and a pair of distinct columns where all four intersections equal `L`? (That is exactly a same-letter 4-corner subgrid.)

## What to use

| Tool | Why |
|------|-----|
| Per-letter column lists | Treat letter `L` as a bipartite graph rows↔columns |
| C4 / “two common neighbors” | Two rows both adjacent to the same two columns |
| `O(k n^2)` mark array | Fast enough; naive bitset-all-row-pairs can TLE if heavy |

## Approach

**Root idea:** four corners with letter `L` ⇔ two rows that share at least two columns containing `L`. Detect that without `O(n^3)`.

**Mental picture:** for letter `A`, paint only the `A` cells. Ask whether any two horizontal lines each hit the same two vertical lines — that forms a rectangle of `A`s at the corners.

**How that becomes the method (C4 / witness array)**

For each letter `L` independently:

1. For each row, collect the list of columns where the cell is `L`.
2. Maintain an `n x n` (or compressed) array `last[c1][c2]` = last row that already “used” the unordered column pair `{c1,c2}` — practically: iterate rows; for each row’s column list, for every pair of columns `(c1,c2)` on that row, if some previous row already claimed `(c1,c2)`, answer `YES`. Mark `(c1,c2)` with the current row.
3. Degree trick for speed: process low-degree side carefully so total pair markings stay `O(n^2)` over the letter (standard bipartite C4 detection in `O(n^2)`).

If no pair is repeated across two rows → `NO`.

Bitset AND of every row pair also works in theory (`popcount >= 2`) but is often tighter on time/memory at `n=3000`, `k=26`; the witness-pair method is the robust `O(k n^2)` approach.

**Data structures:** column lists per row per letter; 2D timestamp/`last` for column pairs (reuse one matrix with a clock to avoid clearing).

## Walkthrough (on a sample)

Letter `A`: top and bottom rows are all `A` → many shared columns → `YES`. Letter `B`: the two middle rows share columns of `B` → `YES`. `C`,`D`,`E` never form two+ shared columns across two rows → `NO`.

## Pitfalls

- Counting a 1×1 or same-row “subgrid”.  
- `O(k n^3)` bitset without enough pruning → TLE.  
- Clearing a huge `n x n` array every letter — use timestamps.

## Complexity

- Time: `O(k n^2)`  
- Space: `O(n^2)` for the pair witness table (or `O(n^2 / w)` with bitsets)

## Key takeaway

**Same-letter corner subgrid ⇔ two rows sharing ≥2 columns of that letter** — detect bipartite C4 in `O(n^2)` per letter.
