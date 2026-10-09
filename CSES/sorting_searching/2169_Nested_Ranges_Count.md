# [2169 — Nested Ranges Count](https://cses.fi/problemset/task/2169)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** sorting, Fenwick tree, coordinate compression, intervals  
**Difficulty / Rating:** Medium–Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given `n` ranges, your task is to count for each range how many other ranges it contains and how many other ranges contain it.

Range `[a,b]` contains range `[c,d]` if `a <= c` and `d <= b`.

### Input

The first input line has an integer `n`: the number of ranges.

After this, there are `n` lines that describe the ranges. Each line has two integers `x` and `y`: the range is `[x,y]`.

You may assume that no range appears more than once in the input.

### Output

First print a line that describes for each range (in the input order) how many other ranges it contains.

Then print a line that describes for each range (in the input order) how many other ranges contain it.

### Constraints

- `1 <= n <= 2e5`
- `1 <= x < y <= 1e9`

### Examples

**Input**
```
4
1 6
2 4
4 8
3 6
```

**Output**
```
2 0 0 0
0 1 0 1
```

---

## What this problem really is

Same nesting relation as Nested Ranges Check, but **count** how many ranges each interval contains / is contained in — not just a bit.

## What to use

| Tool | Why |
|------|-----|
| Sort by left (ties: larger right first) | Same geometric order as the check problem |
| Fenwick / BIT on compressed rights | Count how many seen rights lie in a range |
| Coordinate compression | Rights up to `1e9` |

## Approach

**Root idea:** with ranges sorted by increasing `left` and decreasing `right`, when you process left-to-right, every earlier range starts at or before you and is at least as wide on ties — so “how many contain me?” = how many earlier rights are `>=` my right. When you process right-to-left, “how many do I contain?” = how many later rights are `<=` my right.

**Mental picture:** same sorted stack of intervals; a Fenwick tallies right endpoints you’ve already committed, so each query is a prefix/suffix count on the right-endpoint line.

**Data structures**

- Ranges `{L, R, id}`, sorted as above.
- Compressed ranks of all `R` values.
- Fenwick storing frequencies of rights.

**Contained-by count (second line)** — scan left → right:

1. Query: number of already inserted rights with rank `>= rank(R)` → `contained[id]`.
2. Fenwick-add `+1` at `rank(R)`.

**Contains count (first line)** — clear BIT; scan right → left:

1. Query: number of already inserted rights with rank `<= rank(R)` → `contains[id]`.
2. Fenwick-add `+1` at `rank(R)`.

**Steps**

1. Compress all right endpoints.
2. Sort ranges; run both BIT passes; write answers by `id`.
3. Print the two lines.

## Walkthrough (on a sample)

`[1,6]` contains `[2,4]` and `[3,6]` → **2**. Others **0**.

Contained counts: `[2,4]` and `[3,6]` each contained once by `[1,6]` → `0 1 0 1`.

BIT queries replace the yes/no min/max from the check version with exact frequencies.

## Pitfalls

- Off-by-one in Fenwick prefix vs suffix (`>= R` vs `> R`).
- Forgetting compression or using raw `1e9` indices.
- Same left-endpoint tie-break as the check problem (wider first).
- Counting a range as containing itself.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Nested range counts = sort by left + Fenwick on right endpoints** — one pass each direction for “contains” vs “contained”.
