# [2168 — Nested Ranges Check](https://cses.fi/problemset/task/2168)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** sorting, sweep, Fenwick / set, intervals  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given `n` ranges, your task is to determine for each range if it contains some other range and if some other range contains it.

Range `[a,b]` contains range `[c,d]` if `a <= c` and `d <= b`.

### Input

The first input line has an integer `n`: the number of ranges.

After this, there are `n` lines that describe the ranges. Each line has two integers `x` and `y`: the range is `[x,y]`.

You may assume that no range appears more than once in the input.

### Output

First print a line that describes for each range (in the input order) if it contains some other range (`1`) or not (`0`).

Then print a line that describes for each range (in the input order) if some other range contains it (`1`) or not (`0`).

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
1 0 0 0
0 1 0 1
```

---

## What this problem really is

For each interval, two yes/no questions: does it **contain** another interval? Is it **contained in** another? Ranges are unique; `n` is large.

## What to use

| Tool | Why |
|------|-----|
| Sort by left end | Process nestedness in a consistent order |
| Track max/min right ends seen | Detect containment without pairwise checks |
| Keep original indices | Output in input order |

## Approach

**Root idea:** after sorting ranges by increasing left endpoint (and for equal left, by decreasing right), a range can contain later ranges that end sooner; a range is contained if some earlier range ends later.

**Mental picture:** draw intervals sorted by start. Looking left-to-right, the “widest right edge so far” is a candidate container; looking for contained ranges, a later interval with a smaller right end sits inside an earlier wider one.

**Contains someone (line 1)** — one clean pass:

1. Sort by `left` ascending, then `right` descending; remember `id`.
2. Scan **right-to-left** (from largest left toward smallest): keep the minimum `right` among ranges already scanned (those with larger left). If current `right` is `>=` that minimum, current contains that smaller-right range → mark contains = 1. Then update the minimum with current `right`.

**Contained in someone (line 2):**

1. Same sort.
2. Scan **left-to-right**: keep the maximum `right` among ranges already seen (smaller left, and on ties wider first). If current `right` is `<=` that maximum, something earlier contains current → mark = 1. Then update the maximum.

(Equivalent Fenwick-on-compressed-rights formulations exist; the min/max sweeps suffice for **check** yes/no.)

**Steps**

1. Store `{left, right, id}`.
2. Sort as above; run both sweeps; fill `contains[id]`, `contained[id]`.
3. Print both arrays in id order `0..n-1`.

## Walkthrough (on a sample)

`[1,6]` contains `[2,4]` and `[3,6]` → contains **1**. Others contain nobody → `1 0 0 0`.

Contained: `[2,4]` inside `[1,6]`, `[3,6]` inside `[1,6]` → `0 1 0 1`.

## Pitfalls

- Wrong tie-break when left ends equal (need wider range first for the “contained” scan).
- Comparing ranges to themselves.
- `O(n^2)` pairwise nesting checks — TLE.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Nested-range yes/no = sort by left (wider first on ties) + sweep with running max/min right** to see if anyone nestable sits inside/outside.
