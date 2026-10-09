# [1141 — Playlist](https://cses.fi/problemset/task/1141)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** two pointers, sliding window, set / map  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a playlist of a radio station since its establishment. The playlist has a total of `n` songs.

What is the longest sequence of successive songs where each song is unique?

### Input

The first input line contains an integer `n`: the number of songs.

The next line has `n` integers `k1, k2, ..., kn`: the id number of each song.

### Output

Print the length of the longest sequence of unique songs.

### Constraints

- `1 <= n <= 2e5`
- `1 <= ki <= 1e9`

### Examples

**Input**
```
8
1 2 1 3 2 7 4 2
```

**Output**
```
5
```

---

## What this problem really is

Longest **contiguous** subarray with all distinct values (maximum length of a duplicate-free window).

## What to use

| Tool | Why |
|------|-----|
| Two pointers (`L`,`R`) | Expand right; shrink left when a duplicate enters |
| `set` / `map` of values in window | Detect duplicates; `map` can store last index |
| Or `last[id] = index` map | Jump `L` past the previous occurrence |

## Approach

**Root idea:** maintain a window `[L,R]` that always has unique songs. Move `R` from left to right; when `k[R]` is already inside, advance `L` until that duplicate is gone. Track max `R-L+1`.

**Mental picture:** a growing segment of the playlist; when a song repeats, cut the start forward just past the earlier copy so every id in the segment is unique again.

**Data structures**

- `set<int> in_window`, or `map`/`unordered_map` from song id → last index.
- Pointers `L = 0`, answer `best = 0`.

**Steps (set version)**

1. For `R = 0..n-1`:
   - While `k[R]` is already in the set: erase `k[L]`, `L++`.
   - Insert `k[R]`; `best = max(best, R-L+1)`.
2. Print `best`.

**Last-index version:** store previous index of each id; on seeing a repeat, `L = max(L, prev[k[R]]+1)` before updating `prev` and `best`.

## Walkthrough (on a sample)

`1 2 1 3 2 7 4 2`

- Window grows `1,2` then third `1` forces drop first `1` → `[2,1]`…
- Continues to `1,3,2,7,4` length **5** (indices of `1 3 2 7 4`).
- Final `2` forces shrink past the earlier `2`.

Max **5**.

## Pitfalls

- Subsequence instead of **subarray** (must be successive).
- Shrinking with a set but erasing the wrong end.
- Last-index method: forgetting `L = max(L, …)` (only move forward).
- Song ids up to `1e9` — hash/map, not a big array.

## Complexity

- Time: `O(n log n)` with `set`/`map`, expected `O(n)` with hash  
- Space: `O(n)`

## Key takeaway

**Longest contiguous unique segment = sliding window** — expand right, move left past duplicates, track max length.
