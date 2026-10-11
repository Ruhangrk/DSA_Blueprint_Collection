# [3416 — All Letter Subgrid Count II](https://cses.fi/problemset/task/3416)

**Platform:** CSES  
**Source:** CSES Problem Set — Counting Problems  
**Tags:** grid, two pointers, inclusion  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a grid of letters. Your task is to calculate the number of rectangle subgrids that contain all the letters.

### Input

The first line has two integers `n` and `k`: the size of the grid and the number of letters. The letters are the first `k` uppercase letters.

After this, there are `n` lines that describe the grid. Each line has `n` letters.

### Output

Print the number of subgrids.

### Constraints

- `1 <= n <= 500`
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
70
```

---

## What this problem really is

Count axis-aligned **rectangles** that contain at least one of each of the `k` letters. `n<=500` allows `O(n^3)`-ish solutions.

## What to use

| Tool | Why |
|------|-----|
| Fix left/right columns | Standard `O(n^3)` grid enumeration |
| Freq of letters in the vertical strip | Two pointers on rows / grow bottom |
| `missing` counter | Know when all `k` letters are present |

## Approach

**Root idea:** enumerate the left and right borders of the rectangle (`O(n^2)` pairs). Compress the strip between them into a sequence of rows (each row is a bitmask or count of which letters appear in that row segment). Then count the number of vertical `[top..bottom]` intervals whose union of letters is all `k` — classic two pointers on that `n`-length sequence.

**Mental picture:** pick left and right walls; look at the hallway between them. Slide a vertical window until every letter has shown up in the hallway window; all bottoms further down still work for a fixed top (or vice versa).

**How that becomes the method**

1. For `L = 0..n-1`:  
2. Clear row-masks. For `R = L..n-1`:  
   - Update each row’s letter set with column `R`.  
   - Now each row has a bitmask of letters present in `[L..R]`.  
   - Two-pointer / prefix: count pairs `(t,b)` where OR of masks `t..b` has `k` bits. With `k<=26`, maintain freq of bits while moving `b`, and for each `b` find minimal `t` — add the number of valid tops.  
3. Sum all.

**Data structures:** `rowMask[n]`; `cnt[26]`; 64-bit answer.

## Walkthrough (on a sample)

Seventy rectangles cover all three letters.

## Pitfalls

- Restarting letter counts wrong when extending `R`.  
- Squares-only logic from problem I.  
- `O(n^4)` without strip compression.

## Complexity

- Time: `O(n^3 * k)` roughly (fits `n=500`)  
- Space: `O(n)`

## Key takeaway

**Rectangles with all letters = fix left/right, then two-pointer on row letter-masks.**
