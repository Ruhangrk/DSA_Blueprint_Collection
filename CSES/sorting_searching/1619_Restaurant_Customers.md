# [1619 — Restaurant Customers](https://cses.fi/problemset/task/1619)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** sweep line, events, sorting  
**Difficulty / Rating:** Easy–Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given the arrival and leaving times of `n` customers in a restaurant.

What was the maximum number of customers in the restaurant at any time?

### Input

The first input line has an integer `n`: the number of customers.

After this, there are `n` lines that describe the customers. Each line has two integers `a` and `b`: the arrival and leaving times of a customer.

You may assume that all arrival and leaving times are distinct.

### Output

Print one integer: the maximum number of customers.

### Constraints

- `1 <= n <= 2e5`
- `1 <= a < b <= 1e9`

### Examples

**Input**
```
3
5 8
2 4
3 9
```

**Output**
```
2
```

---

## What this problem really is

Given `n` half-open (or closed-at-start) intervals on a line, find the maximum number of intervals covering any single moment.

## What to use

| Tool | Why |
|------|-----|
| Event sweep | Turn intervals into `+1` at arrival, `-1` at leaving |
| Sort events | Process time in order; track running count |
| Tie-break | Leave before arrive at the same time if ties existed (here times are distinct) |

## Approach

**Root idea:** you only need the count at event times. Sort all arrivals and leavings; scan left to right, maintaining how many customers are currently inside; track the max.

**Mental picture:** a door counter — `+1` when someone walks in, `-1` when they walk out; watch the peak reading.

**Data structures**

- Vector of pairs `(time, delta)` with `delta = +1` for arrival, `-1` for leaving.
- Sort by `time` ascending (if times could tie: process `-1` before `+1` so someone leaving frees the seat first).

**Steps**

1. For each customer `(a,b)`: push `(a, +1)`, `(b, -1)`.
2. Sort events.
3. `cur = 0`, `ans = 0`; for each event: `cur += delta`, `ans = max(ans, cur)`.
4. Print `ans`.

## Walkthrough (on a sample)

Events: `(2,+1), (3,+1), (4,-1), (5,+1), (8,-1), (9,-1)`.

- `t=2`: cur `1`  
- `t=3`: cur `2` → peak **2**  
- `t=4`: cur `1`  
- `t=5`: cur `2`  
- … down to `0`.

Max stays **2**.

## Pitfalls

- Treating the whole timeline as an array indexed by time — times go to `1e9`.
- Forgetting that leaving decreases the count (only counting arrivals).
- If times were not distinct: sort leaves before arrivals at equal time when the problem treats `[a,b)` style presence.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Max concurrent intervals = sweep line with +1/−1 events**, sort by time, track running count.
