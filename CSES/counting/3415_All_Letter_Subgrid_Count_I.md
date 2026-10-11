# [3415 — All Letter Subgrid Count I](https://cses.fi/problemset/task/3415)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** grid, two pointers, coverage  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a grid of letters. Your task is to calculate the number of square subgrids that contain all the letters.

### Input

The first line has two integers `n` and `k`: the size of the grid and the number of letters. The letters are the first `k` uppercase letters.

After this, there are `n` lines that describe the grid. Each line has `n` letters.

### Output

Print the number of subgrids.

### Constraints

- `1 <= n <= 3000`
- `1 <= k <= 26`

### Examples

**Input**
```
5 3
ABBBC
BBBBC
BCAAA
AAAAA
AAAAA
```

**Output**
```
15
```

---

## What this problem really is

Count squares that include at least one of each of the `k` letters (letters are `A..` first `k`).

## What to use

| Tool | Why |
|------|-----|
| Fix bottom-right / side length | Enumerate squares carefully in `O(n^2)` |
| Last-seen positions per letter | Track coverage inside a growing square |
| Freq of letters in window | `missing` counter when freq hits 0/1 |

## Approach

**Root idea:** a square is good iff its multiset of cells covers all `k` letters. For each possible top-left or bottom-right, find the minimal side that already covers all letters, then every larger side up to the border also works (monotonicity).

**Mental picture:** grow a square from a corner like a camera aperture; once every letter type has entered the frame, all bigger frames from that corner stay good.

**How that becomes the method**

1. For each cell `(i,j)` as bottom-right corner:  
2. Expand side `s = 1,2,...` while staying in-grid, maintaining letter frequencies in the square `(i-s+1..i, j-s+1..j)` — expanding adds an L-shaped strip.  
3. When `#distinct` among the `k` relevant letters first hits `k`, all sides `s..s_max` count → add `s_max - s + 1`.  
4. Naive expand is `O(n^3)`. Optimize with two pointers / precomputed next occurrence: for each BR, binary search minimal `s` using 2D range “has letter” queries (sparse OR / last positions).

Efficient pattern: for each of `k` letters, precompute nearest occurrence in the up-left rectangle; minimal covering square side from BR is the max over letters of Chebyshev distance to that letter’s nearest cell in the valid region — then count.

**Data structures:** grid; `last[k]` / 2D RMQ of presence; 64-bit answer.

## Walkthrough (on a sample)

Fifteen squares contain `A`,`B`, and `C` at least once.

## Pitfalls

- Requiring only distinct count without restricting to the first `k` letters.  
- `O(n^3)` without monotonic skip.  
- Rectangles (problem II) vs squares.

## Complexity

- Time: target `O(k n^2)`  
- Space: `O(n^2)` or `O(k n)`

## Key takeaway

**Squares containing all letters = for each corner, minimal covering side, then all larger sides** (monotonic).
