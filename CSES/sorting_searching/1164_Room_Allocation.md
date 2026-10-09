# [1164 — Room Allocation](https://cses.fi/problemset/task/1164)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** greedy, priority queue, sweep, interval graph coloring  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There is a large hotel, and `n` customers will arrive soon. Each customer wants to have a single room.

You know each customer's arrival and departure day. Two customers can stay in the same room if the departure day of the first customer is earlier than the arrival day of the second customer.

What is the minimum number of rooms that are needed to accommodate all customers? And how can the rooms be allocated?

### Input

The first input line contains an integer `n`: the number of customers.

Then there are `n` lines, each of which describes one customer. Each line has two integers `a` and `b`: the arrival and departure day.

### Output

Print first an integer `k`: the minimum number of rooms required.

After that, print a line that contains the room number of each customer in the same order as in the input. The rooms are numbered `1, 2, ..., k`. You can print any valid solution.

### Constraints

- `1 <= n <= 2e5`
- `1 <= a <= b <= 1e9`

### Examples

**Input**
```
3
1 2
2 4
4 4
```

**Output**
```
2
1 2 1
```

---

## What this problem really is

Assign intervals to the minimum number of resources (rooms) so that overlapping stays never share a room. Same departure and next arrival **conflict** (`departure < arrival` required to reuse — so same-day turnaround is **not** allowed).

## What to use

| Tool | Why |
|------|-----|
| Sort customers by arrival | Process in time order |
| Min-heap of `(departure, room_id)` | Free the earliest-ending occupied room |
| Room id array by customer index | Reconstruct assignment |

## Approach

**Root idea:** when a customer arrives, reuse any room whose last occupant already departed **strictly before** this arrival; prefer the room that frees earliest. If none, open a new room. That uses the minimum number of rooms (equal to max concurrent overlapping stays).

**Mental picture:** a pool of keys; each key remembers when its guest leaves. New guest takes the key that became free soonest, or gets a brand-new key.

**Data structures**

- Customers `{arrival, departure, index}`, sorted by `arrival`.
- Priority queue (min-heap) of `{departure, room}` for rooms currently in use.
- `ans[index] = room`; `int rooms_used = 0`.

**Steps**

1. Sort by arrival day.
2. For each customer in that order:
   - While heap top has `departure < arrival`, that room is free — pop and remember you can reuse (or pop all free; typically reuse the earliest free).
   - Simpler: if heap not empty and `heap.top().departure < arrival`, reuse that room id (pop, assign same room, push new departure).
   - Else: `rooms_used++`, assign new room id = `rooms_used`, push `{departure, room}`.
3. Print `rooms_used`, then `ans[0..n)` in input order.

## Walkthrough (on a sample)

Customers: A`[1,2]`, B`[2,4]`, C`[4,4]`.

- A arrives → room **1**, heap `(dep 2, room 1)`.
- B arrives day `2`: A’s dep `2` is **not** `< 2` → need room **2**.
- C arrives day `4`: A finished (`2 < 4`) → reuse room **1**.

Rooms needed **2**; assignment `1 2 1`.

## Pitfalls

- Allowing reuse when `departure == arrival` — problem requires **strictly earlier** departure.
- Sorting by departure only and forgetting to output per-customer rooms.
- Using max-heap or wrong comparison on days.
- Computing only `k` (max overlap) without constructing an assignment — both are required.

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Min rooms + assignment = sort by arrival + min-heap of room free-times**; reuse if `last_departure < arrival`, else open a new room.
