# [2162 — Josephus Problem I](https://cses.fi/problemset/task/2162)

**Platform:** CSES  
**Source:** CSES Problem Set — Sorting and Searching  
**Tags:** queue, linked list, josephus, simulation  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a game where there are `n` children (numbered `1, 2, ..., n`) in a circle. During the game, every other child is removed from the circle until there are no children left. In which order will the children be removed?

### Input

The only input line has an integer `n`.

### Output

Print `n` integers: the removal order.

### Constraints

- `1 <= n <= 2e5`

### Examples

**Input**
```
7
```

**Output**
```
2 4 6 1 5 3 7
```

---

## What this problem really is

Classic Josephus with **every 2nd** child eliminated (`k = 1` skip in 0-based “count every 2nd” terms). Output the full elimination order, not only the survivor. `n` up to `2e5` needs `O(n)` or `O(n log n)`.

## What to use

| Tool | Why |
|------|-----|
| Queue / deque simulation | “Skip one, remove one” maps cleanly to rotate |
| Or doubly linked list / `std::list` | Erase every other node in a circle |
| Indexed Fenwick / order-stat tree | Also works; more natural for general `k` (see Josephus II) |

## Approach

**Root idea:** maintain the circle as a queue of living children. Repeatedly: take the front (skip — they survive this count), move them to the back; take the new front and eliminate them.

**Mental picture:** kids in a line that wraps; you always pass over the first in line (send to the end) and show the next one the door.

**Data structures**

- `queue<int>` (or deque) holding `1..n` in order.

**Steps**

1. Push `1..n` into the queue.
2. While the queue is not empty:
   - Pop front `a`, push `a` to back (skipped).
   - Pop front `b`, print `b` (removed).
3. When one child remains, the same rule removes them on the next iteration (skip… only one left so after skip they’re at front and removed — verify with `n=1`: need care).

For `n=1`, answer is just `1`. Implementation: while size `> 0`, if size is `1` print and break; else skip-then-remove.

**Alternative O(n):** use a list/iterator walking two steps each time and erasing.

## Walkthrough (on a sample)

`n=7`: circle `1 2 3 4 5 6 7`.

Skip `1`, remove `2`; skip `3`, remove `4`; skip `5`, remove `6`; skip `7`, remove `1`; skip `3`, remove `5`; skip `7`, remove `3`; remove `7`.

Order: `2 4 6 1 5 3 7`.

## Pitfalls

- `O(n^2)` vector erase in the middle — TLE at `2e5`.
- Off-by-one on who is skipped vs removed.
- Forgetting the last remaining child in the output.

## Complexity

- Time: `O(n)` with queue (each child enqueued/dequeued `O(1)` times amortized)  
- Space: `O(n)`

## Key takeaway

**Josephus every-other removal order = queue simulation: move front to back, then pop-and-print** until empty.
