# [1629 — Movie Festival](https://cses.fi/problemset/task/1629)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** greedy, interval scheduling, sorting  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

In a movie festival `n` movies will be shown. You know the starting and ending time of each movie. What is the maximum number of movies you can watch entirely?

### Input

The first input line has an integer `n`: the number of movies.

After this, there are `n` lines that describe the movies. Each line has two integers `a` and `b`: the starting and ending times of a movie.

### Output

Print one integer: the maximum number of movies.

### Constraints

- `1 <= n <= 2e5`
- `1 <= a < b <= 1e9`

### Examples

**Input**
```
3
3 5
4 9
5 8
```

**Output**
```
2
```

---

## What this problem really is

Classic **interval scheduling**: pick the maximum number of non-overlapping intervals (watch one movie at a time; next may start when the previous ends).

## What to use

| Tool | Why |
|------|-----|
| Sort by ending time | Greedy choice: always take the movie that frees you earliest |
| Single scan | After sort, take a movie if `start >= last_end` |
| Greedy proof intuition | Leaving earlier never blocks a schedule that a later-ending pick would allow |

## Approach

**Root idea:** among movies you could watch next, the one that **ends soonest** leaves the most room for future films. Sort all movies by end time; scan and take whenever it doesn’t overlap the last taken end.

**Mental picture:** always grab the show that finishes first among what’s still feasible — get back in line for the next pick sooner.

**Data structures**

- Vector of pairs `(end, start)` (or `(start,end)` sorted by end).
- `last_end = 0`, `ans = 0`.

**Steps**

1. Read movies; sort by increasing `end` (break ties arbitrarily).
2. For each movie `(start, end)` in that order: if `start >= last_end`, take it → `ans++`, `last_end = end`.
3. Print `ans`.

## Walkthrough (on a sample)

Movies: `[3,5], [4,9], [5,8]`. Sorted by end: `[3,5], [5,8], [4,9]`.

- Take `[3,5]` (`last_end=5`).
- `[5,8]`: `5 >= 5` → take (`last_end=8`).
- `[4,9]`: `4 < 8` → skip.

Answer **2**.

## Pitfalls

- Sorting by start time instead of end — wrong greedy.
- Using strict `start > last_end` when equal times are allowed (here `a < b` only; touching at an endpoint is fine with `>=`).
- DP over all pairs — unnecessary and too slow at `2e5`.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Max non-overlapping movies = sort by ending time, then take every film that starts at or after your current free time.**
